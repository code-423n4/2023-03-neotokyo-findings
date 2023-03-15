# Summary

| Id | Title |
| -- | ----- |
| 1 | Unnecessary check for AssetType |
| 2 | AssetType has value from 0-3, check for `uint8(_assetType) > 4` still allow `_assetType = 4` |
| 3 | Division before multiplication |
| 4 | Formula to calculate point in LP pool can be simplified | 

# 1. Unnecessary check for AssetType

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205

## Detail
ENUM in Solidity will revert automatically if it is assigned a value that is not predefined. So the check for AssetType in `stake()` and `withdraw()` functions are unnecessary.
```solidity
if (uint8(_assetType) > 4) { // @audit unnecessary check
    revert InvalidAssetType(uint256(_assetType));
}
```

## Recommendation
Remove the AssetType checks.


# 2. AssetType has value from 0-3, check for `uint8(_assetType) > 4` still allow `_assetType = 4`

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205

## Detail
AssetType ENUM only has 4 predefined values, so its value is from 0-3. However the check in function `stake()` and `withdraw()` are still allow `_assetType = 4`. 
```solidity
if (uint8(_assetType) > 4) { // @audit value 4 is still allowed
    revert InvalidAssetType(uint256(_assetType));
}
```

## Recommendation
Consider updating the check or removing it since it is unnecessary anyway.

# 3. Division before multiplication

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1388

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1623

## Detail
Performing multiplication before division is generally better to avoid loss of precision because Solidity integer division might truncate. There are multiple places in the codebase this issue exists. For example

```solidity
unchecked {
    // @audit division before multiplication
    uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
    uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
    share /= _PRECISION;
    daoShare /= _PRECISION;
    return ((share - daoShare), daoShare);
}
```

## Recommendation
Consider doing all multiplication before division to avoid truncation.

# 4. Formula to calculate point in LP pool can be simplified

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1623

## Detail

Formula to calculate `points` in functions `stake()` and `withdraw()` can be simplified since `_DIVISOR = 100`.
```solidity
unchecked {
  uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
```

## Recommendation
So the formula can be simplified to 
```soldiity
uint256 points = amount * lpPosition.multiplier / 1e18;
```

