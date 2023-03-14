## Low Issues

| Number | Issue | Instances |
|---|---|---|
| Low-01 | Denial of Service when calling the `stake()` function when setting the startTime greater than zero | 1 |
| Low-02 | Preventing the user from getting the reward due to the lost precision | 1 |
| Low-03 | The user will get the reward without staking time | 1 |
| Low-04 | Lack of zero address checks | 2 |
| Low-05 | The `getReward()` function can be called by anyone to claim rewards for another user | 1 |

### [L-01] Denial of Service when calling the `stake()` function when setting the startTime greater than zero

## Line References

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1226
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1320
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1819

## Description

The `stake()` function will call the `getReward()` function to set the `lastRewardTime` state.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1226

The `stake()` function will be reverted when the `window.startTime` of the first index is greater than `0` which is set in the `configurePools()` function.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1310-L1320
```solidity=1310
uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
uint256 windowCount = pool.rewardCount;
for (uint256 i; i < windowCount; ) {
    RewardWindow memory window = pool.rewardWindows[i];

    /*
        If the last reward time is less than the starting time of this 
        window, then the reward was accrued in the previous window.
    */
    if (lastPoolRewardTime < window.startTime) {
        uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;
```

## Recommended Mitigation Steps

Adding the restriction when setting the `window.startTime` of the first index in the `configurePools()` function is not greater than `0`.
```solidity=1819
function configurePools (
    PoolConfigurationInput[] memory _inputs
) hasValidPermit(UNIVERSAL, CONFIGURE_POOLS) external {
    for (uint256 i; i < _inputs.length; ) {
        uint256 poolRewardWindowCount = _inputs[i].rewardWindows.length;
        _pools[_inputs[i].assetType].rewardCount = poolRewardWindowCount;
        _pools[_inputs[i].assetType].daoTax = _inputs[i].daoTax;

        // Set the pool reward window details by populating the mapping.
        uint256 lastTime;
        for (uint256 j; j < poolRewardWindowCount; ) {
            ///////////// Mitigation Steps //////////////
            if(j==0 && _inputs[i].rewardWindows[j].startTime > 0){
              revert RewardWindowTimesMustLessThanZero();
            }
            /////////////////////////////////////////////
            _pools[_inputs[i].assetType].rewardWindows[j] =
                _inputs[i].rewardWindows[j];

            // Revert if an invalid pool configuration is supplied.
            if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
                revert RewardWindowTimesMustIncrease();
            }
            lastTime = _inputs[i].rewardWindows[j].startTime;
            unchecked { j++; }
        }
        unchecked { ++i; }
    }
}
```

### [Low-02] Preventing the user from getting the reward due to the lost precision

## Line References
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388-L1392

## Description
The `getPoolReward()` function is used to calculate the amount of reward that the user will get.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388-L1392
```solidity=1388
uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
share /= _PRECISION;
daoShare /= _PRECISION;
return ((share - daoShare), daoShare);
```
As a result of dividing before multiplying, the reward will be less than it should be.

## Recommended Mitigation Steps
Modify the function to multiply before dividing.

### [Low-03] The user will get the reward without staking time

## Line References
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L962
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1016
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1140
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737

## Description

The `stake()` function is used to stake the user's asset and earn more reward points depending on the `_timelock` state, which is set by the `configureTimelockOptions()` function.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L962
```solidity=962
uint256 timelockDuration = _timelock >> 128;
uint256 timelockMultiplier = _timelock & type(uint128).max;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737
```solidity=1737
function configureTimelockOptions (
    AssetType _assetType,
    uint256[] memory _timelockIds,
    uint256[] memory _encodedSettings
) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
    for (uint256 i; i < _timelockIds.length; ) {
        timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];
        unchecked { ++i; }
    }
}
```

However, the duration can be set to any number below `type(uint256).max()`, for example.
```solidity=
uint256 _timelock = uint256(bytes32(type(uint256).max << 128) ^ bytes32(type(uint256).max));
/// which is 340282366920938463463374607431768211455
```

Any number below `340282366920938463463374607431768211455` will make the `timelockDuration` state be 0 and the `timelockMultiplier` be greater than`0`. 

Resulting in breaking the business logic that the `timelockMultiplier` will be relevant with the `timelockDuration`.

## Recommended Mitigation Steps

Add a validation check to prevent the value below `340282366920938463463374607431768211455` from being passed into the `configureTimelockOptions()` function.
```solidity=1737
function configureTimelockOptions (
    AssetType _assetType,
    uint256[] memory _timelockIds,
    uint256[] memory _encodedSettings
) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
    uint256 upperBound = uint256(bytes32(type(uint256).max << 128) ^ bytes32(type(uint256).max));
    for (uint256 i; i < _timelockIds.length; ) {
        if(_encodedSettings[i] <= upperBound){
          revert DoNotSetTooHighNumber();
        }
        timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];
        unchecked { ++i; }
    }
}
```

### [Low-04] Lack of zero address checks

## Line References
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1708
## Description
If these variable get configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

## Recommended Mitigation Steps
Add a validation check when setting the address in functions.

### [Low-05] The `getReward()` function can be called by anyone to claim rewards for another user

## Line References

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114-L129

## Description

In the current implementation of BYTES2, the `getReward()` function can be called by anyone to claim rewards for another user.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114-L129
```solidity=114
function getReward (
    address _to
) external {
    (
        uint256 reward,
        uint256 daoCommision
    ) = IStaker(STAKER).claimReward(_to);

    // Mint both reward BYTES and the DAO tax to targeted recipients.
    if (reward > 0) {
        _mint(_to, reward);
    }
    if (daoCommision > 0) {
        _mint(TREASURY, daoCommision);
    }
}
```

Resulting in the reward will be claimed arbitrarily.

## Recommended Mitigation Steps
Add the code below to prevent the call from other users.

```solidity=114
function getReward (
    address _to
) external {
    if (msg.sender != S1_CITIZEN && msg.sender != STAKER){
      revert DontCallForOther(_to);
    } 
    (
        uint256 reward,
        uint256 daoCommision
    ) = IStaker(STAKER).claimReward(_to);

    // Mint both reward BYTES and the DAO tax to targeted recipients.
    if (reward > 0) {
        _mint(_to, reward);
    }
    if (daoCommision > 0) {
        _mint(TREASURY, daoCommision);
    }
}
```

## Non-Critical Issues

| Number |	Issues | Instances |
|---|---|---|
|[N-01]| Critical State Changes Should Use Two-step Procedure	| 2 | 
|[N-02]| Function writing that does not comply with the Solidity Style Guide	| 2 |
|[N-03]| Missing return in NatSpec | 1 |
|[N-04]| Compliance with Solidity Style rules in Upper Case expressions	| 2 |
|[N-05]| Lock pragmas to specific compiler version	| 2 |
| [N-06] | Lack of array length check in the configuration function | 4 | 

### [N-01] Critical Address Changes Should Use Two-step Procedure

## Line References

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162-L166

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173-L177

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1708-L1715

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1721-L1723

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737-L1746

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1760-L1773

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1783-1791

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1802-L1810

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1819-L1842

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1851-L1857

## Description
The critical procedures should be carried out in two steps.

## Recommended Mitigation Steps
We suggest adding two step procedure on the critical functions.

### [N-02] Function writing that does not comply with the Solidity Style Guide

## Line References

All Contracts.

## Description
Organizing functions within a contract can help users know which ones they can call, but some contracts in the project do not follow this practice.

Source: https://docs.soliditylang.org/en/v0.8.19/style-guide.html

## Recommended Mitigation Steps
To improve code readability, it's recommended that functions are:

Grouped by their visibility and ordered as follows:
- Constructor
- Receive function (if it exists)
- Fallback function (if it exists)
- External
- Public
- Internal
- Private

Within each grouping, view and pure functions should be placed last.

### [N-03] Missing return in NatSpec

## Line References

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1257-L1267

## Description
The NatSpec comment block for the function is incomplete, as it does not include a `return` parameter to describe the format and meaning of the function's return values.

```
File: NeoTokyoStaker.sol

	/**
		This function supports retrieving the reward and tax earned by a particular 
		`_recipient` on a specific pool of type `_assetType`.
		@param _assetType The type of the asset to calculate rewards for.
		@param _recipient The recipient of the reward.
	*/
	function getPoolReward (
		AssetType _assetType,
		address _recipient
	) public view returns (uint256, uint256) {
```

## Recommended Mitigation Steps

Adding `return` parameter in NatSpec comments.

### [N-04] Compliance with Solidity Style rules in Upper Case expressions

## Line References

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L49

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L52

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L232

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L249

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L255

## Description

Variables that are not declared as constants or immutable should be named using the lower_case format.

```
File: BYTES2.sol

49:     address public STAKER;

52:	address public TREASURY;
```

```
File: NeoTokyoStaker.sol

232:	address public LP;
    
249:	uint256 public VAULT_CAP;

255:	uint256 public NO_VAULT_CAP;
```

## Recommended Mitigation Steps
We suggest using the lower_case format when naming variables that are not declared as constants or immutable, as this helps distinguish them from constant variables and complies with Solidity naming conventions.

### [N-05] Lock pragmas to specific compiler version

## Line References
All Contracts.

## Description
All the contracts in scope are floating the pragma version.

## Recommended Mitigation Steps
Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

### [N-06] Lack of array length check in the configuration function

## Line References
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1760
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1783
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1802

## Description

The configuration functions that have input parameters as arrays will be reverted due to the lack of a validation length check.

## Recommended Mitigation Steps
Add a validation length check when input parameters are arrays.
