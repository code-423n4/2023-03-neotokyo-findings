## QA REPORT

| |Issue|
|-|:-|
| [01] | `NeoTokyoStaker.stake` AND `NeoTokyoStaker.withdraw` FUNCTIONS SHOULD CHECK `uint8(_assetType) > 3)` INSTEAD OF `uint8(_assetType) > 4)` |
| [02] | STAKING FUNCTIONALITY IS UNAVAILABLE WHEN `block.timestamp` AND `_pools[_assetType].rewardWindows[0].startTime` ARE SAME |
| [03] | `unchecked` USAGE CAN EVENTUALLY BECOME UNSAFE |
| [04] | BYTES 2.0 AND BYTES 1.0 TOKENS USE SAME NAME AND SYMBOL |
| [05] | ADDITIONAL LP TOKENS CANNOT BE STAKED WITH DIFFERENT `timelockMultiplier`, WHICH CAN BE INCONVENIENT TO USERS |
| [06] | USERS ARE UNABLE TO WITHDRAW SPECIFIED BYTES TOKEN AMOUNTS OVER TIME |
| [07] | `NeoTokyoStaker.stake` FUNCTION'S COMMENT IS NOT COMPLETE FOR SITUATION WHERE ASSET BEING STAKED IS BYTES |
| [08] | FUNCTIONS IN `NeoTokyoStaker` CONTRACT DO NOT FOLLOW CHECKS-EFFECTS-INTERACTIONS PATTERN |
| [09] | MISSING `address(0)` CHECKS FOR CRITICAL ADDRESS INPUTS |
| [10] | VULNERABILITIES IN VERSION 4.4.2 OF `@openzeppelin/contracts-upgradeable` AND VERSION 4.3.1 OF `@openzeppelin/contracts` |
| [11] | CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS |
| [12] | HARDCODED STRINGS THAT HAVE SPECIAL MEANINGS CAN BE REPLACED WITH CONSTANTS |
| [13] | FLOATING PRAGMAS |
| [14] | WORD TYPING TYPO |
| [15] | INCOMPLETE NATSPEC COMMENTS |
| [16] | MISSING NATSPEC COMMENTS |

## [01] `NeoTokyoStaker.stake` AND `NeoTokyoStaker.withdraw` FUNCTIONS SHOULD CHECK `uint8(_assetType) > 3)` INSTEAD OF `uint8(_assetType) > 4)`
The `uint8` representation of the following `AssetType` enum's maximum is 3. Yet, calling the `NeoTokyoStaker.stake` and `NeoTokyoStaker.withdraw` functions below would revert if `uint8(_assetType)` is more than 4. In other words, if `uint8(_assetType)` equals 4, calling these functions would not revert. This is inconsistent with the `AssetType` enum in which its maximum cannot be 4. To improve these sanity checks, please consider replacing `uint8(_assetType) > 4)` with `uint8(_assetType) > 3)` in these functions.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L266-L271
```solidity
	enum AssetType {
		S1_CITIZEN,
		S2_CITIZEN,
		BYTES,
		LP
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196-L1255
```solidity
	function stake (
		AssetType _assetType,
		uint256 _timelockId,
		uint256,
		uint256,
		uint256
	) external nonReentrant {

		// Validate that the asset being staked is of a valid type.
		if (uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}

		...
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1659-L1698
```solidity
	function withdraw (
		AssetType _assetType,
		uint256
	) external nonReentrant {

		/*
			Validate that the asset being withdrawn is of a valid type. BYTES may not 
			be withdrawn independently of the Citizen that they are staked into.
		*/
		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}

		...
	}
```

## [02] STAKING FUNCTIONALITY IS UNAVAILABLE WHEN `block.timestamp` AND `_pools[_assetType].rewardWindows[0].startTime` ARE SAME
The comment of the following `RewardWindow` struct indicates that its `startTime` is "the time at which the daily reward activated". Yet, calling the `NeoTokyoStaker.stake` function below reverts when `block.timestamp` and `_pools[_assetType].rewardWindows[0].startTime` are the same though the pool would be considered as active at `_pools[_assetType].rewardWindows[0].startTime`. This means that the staking functionality becomes unavailable to users when `block.timestamp` and `_pools[_assetType].rewardWindows[0].startTime` are the same.

As a mitigation, please consider updating the `NeoTokyoStaker.stake` function to check `_pools[_assetType].rewardWindows[0].startTime > block.timestamp` instead of `_pools[_assetType].rewardWindows[0].startTime >= block.timestamp`.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L287-L293
```solidity
		@param startTime The time at which the daily reward activated.
		@param reward The reward emission rate beginning at `startTime`.
	*/
	struct RewardWindow {
		uint128 startTime;
		uint128 reward;
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196-L1255
```solidity
	function stake (
		AssetType _assetType,
		uint256 _timelockId,
		uint256,
		uint256,
		uint256
	) external nonReentrant {
		...

		// Validate that the asset being staked matches an active pool.
		if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
			revert InactivePool(uint256(_assetType));
		}

		...
	}
```

## [03] `unchecked` USAGE CAN EVENTUALLY BECOME UNSAFE
`unchecked` is used in multiple places of the codebase but can eventually become unsafe. For example, the following `NeoTokyoStaker.claimReward` function executes `totalReward = (s1Reward + s2Reward + lpReward)` in an `unchecked` block. If the corresponding tokens have been staked for a very long time, calling the `NeoTokyoStaker.getPoolReward` function can return very large numbers of reward shares; then executing `s1Reward + s2Reward + lpReward` in the `NeoTokyoStaker.claimReward` function can potentially overflow to an amount that is much smaller than it should be without reverting. In this case, such small number of BYTES token are minted to the user when the user actually is entitled to a much bigger number of BYTES token for the rewards.

As a mitigation, please consider not using `unchecked` in the relevant code. For example, the `NeoTokyoStaker.claimReward` function can be updated to not wrap `totalReward = (s1Reward + s2Reward + lpReward)` and `totalTax = (s1Tax + s2Tax + lpTax)` in the `unchecked` block. In addition, another function can be added and used for claiming a pre-determined maximum number of reward shares if executing `s1Reward + s2Reward + lpReward` ever overflows to revert in the `NeoTokyoStaker.claimReward` function.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1409-L1454
```solidity
	function claimReward (
		address _recipient
	) external returns (uint256, uint256) {
		...

		// Retrieve the `_recipient` reward share from each pool.
		(uint256 s1Reward, uint256 s1Tax) = getPoolReward(
			AssetType.S1_CITIZEN,
			_recipient
		);
		(uint256 s2Reward, uint256 s2Tax) = getPoolReward(
			AssetType.S2_CITIZEN,
			_recipient
		);
		(uint256 lpReward, uint256 lpTax) = getPoolReward(
			AssetType.LP,
			_recipient
		);

		...

		// Calculate total reward and tax.
		uint256 totalReward;
		uint256 totalTax;
		unchecked {
			totalReward = (s1Reward + s2Reward + lpReward);
			totalTax = (s1Tax + s2Tax + lpTax);
		}

		...
	}
```

## [04] BYTES 2.0 AND BYTES 1.0 TOKENS USE SAME NAME AND SYMBOL
As shown by the following code, the name and symbol are `BYTES` for the BYTES 2.0 token, which are the same for the BYTES 1.0 token. This can cause confusion to users because tools like a scanner can display the same meta information for both tokens. Also, this can introduce difficulties for other protocols when integrating with this protocol.

As a mitigation, please consider updating the BYTES 2.0 token to use name and symbol that are different than `BYTES`.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L34
```solidity
contract BYTES2 is PermitControl, ERC20("BYTES", "BYTES") {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/s1/BYTESContract.sol#L798
```solidity
contract BYTESContract is Ownable, ERC20("BYTES", "BYTES") {
```

## [05] ADDITIONAL LP TOKENS CANNOT BE STAKED WITH DIFFERENT `timelockMultiplier`, WHICH CAN BE INCONVENIENT TO USERS
Calling the following `NeoTokyoStaker._stakeLP` function reverts if `stakerLPPosition[msg.sender].multiplier != timelockMultiplier` is true. This means that after staking some LP tokens with a `timelockMultiplier`, the user cannot stake additional LP tokens with a different `timelockMultiplier`. In order to stake additional LP tokens with a different `timelockMultiplier`, the user has to wait until the timelock expires for the previously staked LP tokens and withdraw them, which can be very inconvenient. To improve the user experience, please consider adding a mechanism for staking additional LP tokens with a different `timelockMultiplier`.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1124-L1174
```solidity
	function _stakeLP (
		uint256 _timelock
	) private {
		...

		// Decode the timelock option's duration and multiplier.
		uint256 timelockDuration = _timelock >> 128;
		uint256 timelockMultiplier = _timelock & type(uint128).max;

		// If this is a new stake of this asset, initialize the multiplier details.
		if (stakerLPPosition[msg.sender].multiplier == 0) {
			stakerLPPosition[msg.sender].multiplier = timelockMultiplier;

		// If a multiplier exists already, we must match it.
		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) {
			revert MismatchedTimelock();
		}

		...
	}
```

## [06] USERS ARE UNABLE TO WITHDRAW SPECIFIED BYTES TOKEN AMOUNTS OVER TIME
When staking the BYTES tokens, users can stake various BYTES token amounts over time; however, they have to withdraw the staked BYTES tokens all at once because only the following `NeoTokyoStaker._withdrawS1Citizen` and `NeoTokyoStaker._withdrawS2Citizen` functions, which would transfer all staked BYTES tokens to `msg.sender`, are available. To improve the user experience, please consider adding a mechanism to support the use case for withdrawing specified BYTES token amounts over time.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1459-L1529
```solidity
	function _withdrawS1Citizen () private {
		...
		// Return any staked BYTES.
		if (stakedCitizen.stakedBytes > 0) {
			_assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
		}
		...
		stakedCitizen.stakedBytes = 0;
		...
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1534-L1592
```solidity
	function _withdrawS2Citizen () private {
		...
		// Return any staked BYTES.
		if (stakedCitizen.stakedBytes > 0) {
			_assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
		}
		...
		stakedCitizen.stakedBytes = 0;
		...
	}
```

## [07] `NeoTokyoStaker.stake` FUNCTION'S COMMENT IS NOT COMPLETE FOR SITUATION WHERE ASSET BEING STAKED IS BYTES
The following comment for the `NeoTokyoStaker.stake` function only describes the input parameter for the situation where the asset being staked is an S1 Citizen. Yet, this input parameter is also used as the corresponding `citizenId` when the asset being staked is BYTES. This can confuse and mislead users for calling this function. To avoid such confusion, please consider completing this comment to explain the situation where the asset being staked is BYTES.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1189-L1190
```solidity
		@custom:param If the asset being staked is an S1 Citizen, this is the ID of 
			a Vault to attempt to optionally attach.
```

## [08] FUNCTIONS IN `NeoTokyoStaker` CONTRACT DO NOT FOLLOW CHECKS-EFFECTS-INTERACTIONS PATTERN
The `NeoTokyoStaker._stakeS1Citizen`, `NeoTokyoStaker._stakeS2Citizen`, `NeoTokyoStaker._stakeBytes`, `NeoTokyoStaker._stakeLP`, `NeoTokyoStaker._withdrawS1Citizen`, `NeoTokyoStaker._withdrawS2Citizen`, and `NeoTokyoStaker._withdrawLP` functions execute the following code to transfer the corresponding tokens before updating the relevant states, which do not follow the checks-effects-interactions pattern. To reduce the potential attack surface and increase the level of security, please consider updating these functions to follow the checks-effects-interactions pattern.

```solidity
contracts\staking\NeoTokyoStaker.sol
  897: 		   _assetTransferFrom(S1_CITIZEN, msg.sender, address(this), citizenId);	
  1010: 		_assetTransferFrom(S2_CITIZEN, msg.sender, address(this), citizenId);	
  1057: 		_assetTransferFrom(BYTES, msg.sender, address(this), amount);	
  1137: 		_assetTransferFrom(LP, msg.sender, address(this), amount);	
  1492: 		_assetTransferFrom(S1_CITIZEN, address(this), msg.sender, citizenId);	
  1557: 		_assetTransferFrom(S2_CITIZEN, address(this), msg.sender, citizenId);	
  1618: 		_assetTransfer(LP, msg.sender, amount);	
```

## [09] MISSING `address(0)` CHECKS FOR CRITICAL ADDRESS INPUTS
To prevent unintended behaviors, critical address inputs should be checked against `address(0)`. `address(0)` checks are missing for the `address` input variables in the following constructors. Please consider checking them.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-L85
```solidity
	constructor (
		address _bytes,
		address _s1Citizen,
		address _staker,
		address _treasury
	) {
		BYTES1 = _bytes;
		S1_CITIZEN = _s1Citizen;
		STAKER = _staker;
		TREASURY = _treasury;
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L606
```solidity
	constructor (
		address _bytes,
		address _s1Citizen,
		address _s2Citizen,
		address _lpToken,
		address _identity,
		address _vault,
		uint256 _vaultCap,
		uint256 _noVaultCap
	) {
		BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
		LP = _lpToken;
		IDENTITY = _identity;
		VAULT = _vault;
		...
	}
```

## [10] VULNERABILITIES IN VERSION 4.4.2 OF `@openzeppelin/contracts-upgradeable` AND VERSION 4.3.1 OF `@openzeppelin/contracts`
As shown in the following code in `package.json`, version 4.4.2 of `@openzeppelin/contracts-upgradeable` and version 4.3.1 of `@openzeppelin/contracts` can be used. As described in https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts-upgradeable/4.4.2 and https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts/4.3.1, these versions have vulnerabilities that are related to `ECDSA.recover`, `initializer`, etc. To reduce the potential attack surface and be more future-proofed, please consider upgrading these packages to at least version 4.7.3.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/package.json#L9-L23
```typescript
    "@openzeppelin/contracts-upgradeable": "^4.4.2",
    ...
    "@openzeppelin/contracts": "^4.3.1",
```

## [11] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, a constant can be used instead of the magic number. Please consider replacing the magic numbers, such as `100`, used in the following code with constants.

```solidity
contracts\staking\NeoTokyoStaker.sol
  946: 		uint256 vaultMultiplier = 100;
  962: 		uint256 timelockDuration = _timelock >> 128;
  1016: 	uint256 timelockDuration = _timelock >> 128;
  1022: 	citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
  1077: 	uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
  1098: 	uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
  1113: 	(seasonId << 128) + citizenId,
  1140: 	uint256 timelockDuration = _timelock >> 128;
  1155: 	uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
  1389: 	uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
```

## [12] HARDCODED STRINGS THAT HAVE SPECIAL MEANINGS CAN BE REPLACED WITH CONSTANTS
For better maintainability, the following `Hand of Citadel` and `?` that are hardcoded and have special meanings can be replaced with constants.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L950-L951
```solidity
			if (_stringEquals(class, "Hand of Citadel")) {
				vaultMultiplier = vaultCreditMultiplier["?"];
```

## [13] FLOATING PRAGMAS
It is a best practice to lock pragmas instead of using floating pragmas to ensure that contracts are tested and deployed with the intended compiler version. Accidentally deploying contracts with different compiler versions can lead to unexpected risks and undiscovered bugs. Please consider locking pragmas for the following files.

```solidity
contracts\staking\BYTES2.sol
  2: pragma solidity ^0.8.19;

contracts\staking\NeoTokyoStaker.sol
  2: pragma solidity ^0.8.19;
```

## [14] WORD TYPING TYPO
( Please note that the following instance is not found in https://gist.github.com/Picodes/01427c59b07c651699136589541159a7#nc-1-typos. )

`ctiizen` can be changed to `citizen` in the following comment.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L533-L535
```solidity
		@param timelockOption Data encoding the parameters surrounding the timelock 
			option used in staking the particular asset. Alternatively, this encodes 
			ctiizen information for BYTES staking.
```

## [15] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. The following function misses the `@return` comments. Please consider completing the NatSpec comments for this function.

```solidity
contracts\staking\NeoTokyoStaker.sol
  1264: 	function getPoolReward (
```

## [16] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. The following function misses NatSpec comments. Please consider adding NatSpec comments for this function.

```solidity
contracts\staking\NeoTokyoStaker.sol
  1044: 	function _stakeBytes (
```