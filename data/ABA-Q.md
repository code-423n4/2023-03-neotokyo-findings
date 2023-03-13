# QA Report for Ethos Reserve contest

## Overview
During the audit, 1 low, 1 non-critical and 2 refactoring issues were found.

### Low Risk Issues

Total: 1 issues

|#|Issue|
|-|:-|
| [L-01] | Users reward withdraws may be front run, leading to reward loss |

### Non-critical Issues

Total: 2 instances over 1 issues

|#|Issue|Instances|
|-|:-|:-:|
| [NC-01]| Enum AssetType range validations are incorrect | 2 |

### Refactoring Issues

Total: 5 instances over 2 issues

|#|Issue|Instances|
|-|:-|:-:|
| [R-01]| Enum AssetType range validations are redundant | 2 |
| [R-02]| Use constants for values with special meaning | 3 |

#
## Low Risk Issues (1)
#

### [L-01] Users reward withdraws may be front run, leading to reward loss

##### Description

Rewards are distributed to users by calling `getReward` from `BYTES2.sol`. This function further calls `claimReward` from `NeoTokyoStaker` to determin the user BYTES to be minted and the DAO's.

`claimReward` flows exectuion to `getPoolReward` where the amount is determined as:

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388-L1392sol#L1388
```Solidity
	uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
	uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
	share /= _PRECISION;
	daoShare /= _PRECISION;
	return ((share - daoShare), daoShare);
```

we can see that user BYTES shares also depend on the `pool.totalPoints` size of the specific asset pool.  The higher the value, the less BYTES are due, which is normal for a staking protocol. After each reward distribution, a time variable is also set, to mark the last user reward date.

The issue with this reward collecting mechanism is the timing. If a user has not claimed in a a long period and wishes to do so now, an ill intoned attacker can front Ron the `getReward` operation and deposit into the pool. This increases the total amount of points in the pool thus giving a lower then expected value to user. The less then expected value is relative to what the user would have gained if he would regularly claim rewards.

The attacker gains nothing in this, also he is further required to actually stake the assets, for a minimum of 30 days also. This however does cause damage to the user and loss of rewards. It is also very easy for an attack to execute it.

##### Recommendation

This is an issue by design, besides a system redesign, user can mitigate this by frequently calling the `getReward` function.

## Non-critical Issues (1)
#

### [NC-01] Enum AssetType range validations are incorrect
##### Description

`AssetType` enum is defined as:
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L266-L271
```Solidity
	enum AssetType {
		S1_CITIZEN, // uint8 value: 0
		S2_CITIZEN, // uint8 value: 1
		BYTES,      // uint8 value: 2
		LP          // uint8 value: 3
	}
```
There are several places in code, where this is an input for external functions and it is checked, incorrectly, to be in the Enum value range:
```Solidity
		if (uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
```

In Solidity, Enums start from 0: https://docs.soliditylang.org/en/v0.8.19/types.html#enums
The check `uint8(_assetType) > 4)` should be modified to `uint8(_assetType) > 3)` as it allows the unmapped value 4 to be considered valid.

This does not impact code as there are other checks that invalidate this issue (also see `[R-01]`)

##### Instances (2)

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196-L1207

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1659-L1670

##### Recommendation

Modify `uint8(_assetType) > 4)` to `uint8(_assetType) > 3)` or simply remove the check (as suggested in `[R-01]`)

#
#### Refactoring Issues (2)
#

### [R-01] Enum AssetType range validations are redundant
##### Description

`AssetType` enum is defined as:
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L266-L271
```Solidity
	enum AssetType {
		S1_CITIZEN, // uint8 value: 0
		S2_CITIZEN, // uint8 value: 1
		BYTES,      // uint8 value: 2
		LP          // uint8 value: 3
	}
```
There are several places in code, where this is an input for external functions and it is checked (incorrectly) to be in a specific range.
Example:
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1204-L1207
```Solidity
		// Validate that the asset being staked is of a valid type.
		if (uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
```
As the value is passed as an argument in these cases

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196-L1197
```Solidity
	function stake (
		AssetType _assetType,
        // ...
```

the Solidity compiler already validates that any passed argument is a valid Enum value by doing an explicit cast. If it is not, it will revert via a Panic error:

https://docs.soliditylang.org/en/v0.8.19/types.html#enums

> The explicit conversion from integer checks at runtime that the value lies inside the range of the enum and causes a Panic error otherwise.

A contradictory observation is that, according to best practices

https://docs.soliditylang.org/en/v0.8.19/control-structures.html#panic-via-assert-and-error-via-require

> Properly functioning code should never create a Panic, not even on invalid external input.

_Creating a panic or not on invalid code is outside of the scope of this issue, it is left for the developer to decide._

##### Instances (2)

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196-L1207

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1659-L1670

In this second case, the `uint8(_assetType) > 4)` is redundant.

##### Recommendation

Remove redundant check

#

### [R-02] Use constants for values with special meaning
##### Description

Use constants for values with special meaning when used in code

##### Instances (3)

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L950
String value may be set as a const variable

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1022
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L946

Consider using a const for "DEFAULT_VAULT_VAULE_MULTIPLIER = 100"

##### Recommendation

Save values as a const variables and use them as so.

#
