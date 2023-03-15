# QA Report for Ethos Reserve contest

## Overview
During the audit, 2 non-critical and 2 refactoring issues were found.

### Non-critical Issues

Total: 2 instances over 1 issues

|#|Issue|Instances|
|-|:-|:-:|
| [NC-01]| Enum AssetType range validations are incorrect | 2 |
| [NC-02]| No support for Uniswap V3 LP staking when BYTESv1 uses a V3 pool | 1 |

### Refactoring Issues

Total: 5 instances over 2 issues

|#|Issue|Instances|
|-|:-|:-:|
| [R-01]| Enum AssetType range validations are redundant | 2 |
| [R-02]| Use constants for values with special meaning | 3 |

#


## Non-critical Issues (2)
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
The check `uint8(_assetType) > 4)` should be modified to `uint8(_assetType) > 3)` as it allowes the unmapped value 4 to be considered valid.

This does not impact code as there are other checks that invalidate this issue (also see `[R-01]`)

##### Instances (2)

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196-L1207

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1659-L1670

##### Recommendation

Modify `uint8(_assetType) > 4)` to `uint8(_assetType) > 3)` or simply remove the check (as suggested in `[R-01]`)


### [NC-02] No support for Uniswap V3 LP staking when BYTESv1 uses a V3 pool
##### Description

The `BYTESv2` staking contract is designed to use Uniswap V2 compatible LP tokens only and doesn't not support V3 LP staking (ERC721 LP tokens).
Normally, this would not be mentioned as an issue, but the current `BYTESv1` liquidity is set in a Uniswap V3 LP pool. 
These types of features are usually kept as they are at protocol level.

##### Instances (1)

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

##### Recommendation

Reconsider if downgrading to a V2 LP staking is what it is best for the protocol in terms of consistency.
Document the decision.

#
#### Refactoring Issues ([F])
#


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
