## Use latest Solidity version with a stable pragma statement
[BYTES2.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2), [NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2)

## No need to use `if else` pattern
NeoTokyoStaker.sol - [689-700](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L693-L699)
Refactored code:
```
if (_assetType == AssetType.S1_CITIZEN) {
    return _stakerS1Position[_staker];
} 
if (_assetType == AssetType.S2_CITIZEN) {
    return _stakerS2Position[_staker];
} 
revert UnknowablePosition(uint256(_assetType));	
```

## It is safer to use `safeTransfer/safeTransferFrom` instead of `transfer/transferFrom`
NeoTokyoStaker.sol - [191](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L191), [194](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L194)