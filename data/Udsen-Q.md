## 1. PUT `pool.totalPoints` OUTSIDE THE `unchecked` BLOCK, TO AVOID A POSSIBILITY OF AN OVERFLOW.

In the `_stakeS1Citizen` function of the `NeoTokyoStaker` contract, the total points of the `S1_CITIZEN` asset pool is calculated as follows inside an `unchecked` block as follows:

			pool.totalPoints += citizenStatus.points; 
			
Here the `citizenStatus.points` is calculated using the `identityPoints` variable as follows: 

			citizenStatus.points =
				identityPoints * vaultMultiplier * timelockMultiplier /
				_DIVISOR / _DIVISOR;

`identityPoints` which is assigned from `identityCreditPoints[citizenCreditYield]` is a configurable parameter by the admin.

Since `identityPoints` is a `uint256` value, if by admin mistake is set a very large value close to `(uint256).max` the resulting `citizenStatus.points` will be a very large number as well.

This will prompt the `uint256` variable `pool.totalPoints` to overflow since it get iteratively added on for each `S1_CITIZEN` stake by the stakers.

Hence it recommended to put the `pool.totalPoints += citizenStatus.points;` calculaton outside the `unchecked` block to avoid a possible overflow.
Because the `pool.totalPoints` of the `S1_CITIZEN` asset pool is a critical variable. If it overflows then the entire rewards calculation for the individual stakers will be errorneous. Which will cripple the entire protocl functionality.
Above change will effect (increase) the gas usage of the operation. Yet security aspect is of more importance in this case than the gas amount saved.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L967-L978
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1077-L1080
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1164

## 2. DO NOT PERFORM ARITHMETIC OPERATION INSIDE THE `emit` OF AN `event`.

It is not recommended to perform arithmetic operations inside the `emit` of an `event`.
The `emits` are placed to log the function results for the use of the off-chain tools and not to perform arithmetic operations.
The arithmetic opeations should be performed outside the `emit` and only the result should be logged and emitted in an `event` for the use of off-chain tools.

		emit Stake(
			msg.sender,
			BYTES,
			(seasonId << 128) + citizenId,
			amount
		);
		
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1110-L1115

## 3. CONDUCT THE MULTIPLICATION BEFORE DIVISON WHEN PERFORMING ARITHMETIC OPERATIONS TO AVOID ROUNDING ERROR.

When arithmetic operations are performed in `solidity`, it is recommended to perform the multiplicatons before division.
This is because the division results are rounded down to the neares interger value in `solidity`. which introduces rounding error.
And if the multiplication is followed, it will inflate the rounding error to a much larger error which could be detrimental to the functionality of the protocol.

			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
			
Above line of code can be updated as follows:

			uint256 points = amount * 100 * timelockMultiplier / 1e18 / _DIVISOR;			

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155 
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1623

## 4. `uint8(_assetType)` SHOULD CHECK FOR `> 3` CONDITION INSTEAD OF `>4` CONDITION.

In the `stake` function of the `NeoTokyoStaker` contract, `if (uint8(_assetType) > 4)` condition is checked to validate that the asset being staked is of a valid type.
But the `AssetType` is a `enum` which consists of only four values as depicted beolw:

	enum AssetType {
		S1_CITIZEN,
		S2_CITIZEN,
		BYTES,
		LP
	}
	
Hence the maximum value of the `AssetType` will be `3`. 
According to the given condition if the uint256 value of the `AssetType` equal `4` then the condition will still allow the program to execute without reversion.
But there is no number `4` value in the `AssetType` enum.

if value `4` is passed in by mistake then the `default` case in the `case statement` will be executed which does not assign any function to `_stake` function. which will prompt unexpected code behaviour.

Hence the above condition checked to validate the asset being staked is of a valid type, should be changed as below:

		if (uint8(_assetType) > 3) { 
			revert InvalidAssetType(uint256(_assetType));
		} 

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205-L1207
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668-L1670

## 5. THE `i-1` OF THE `pool.rewardWindows[i - 1].reward` COULD UNDERFLOW IF THE `lastPoolRewardTime = 0`.

In the `getPoolReward` function of the `NeoTokyoStaker` contract, in order to calculate the `currentRewardRate` the following condtional check and the variable assignment is performed.

				if (lastPoolRewardTime < window.startTime) {
					uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;
					
Here the `lastPoolRewardTime` is calculated as below:

			uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
			
So if it is the first time `_recipient` tries to claim the rewards, then his `lastPoolRewardTime` will be zero.
This will make the `if (lastPoolRewardTime < window.startTime)` condition to pass. So if this is the case for the first iteration of the `for` loop where `i = 0`, then the [i-1] of the ` pool.rewardWindows[i - 1].reward`, will underflow.

Hence it is important to perform the `i != 0` condition inside the `if` statement as below:

				if ( i != 0 && lastPoolRewardTime < window.startTime) {
					uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;  

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1319-L1320

## 6. THERE IS NO INPUT VALIDATION FOR THE `daoShare` IN THE POOL CONFIGURATION. HENCE `pool.daoTax < 1` CHECK SHOULD BE PERFORMED.

In the `getPoolReward` function of the `NeoTokyoStaker` contract, the following line of code is used to return the result of the function.

				return ((share - daoShare), daoShare);

Here the `daoShare` is calculated as below:

				uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);

But the `pool.daoTax` value is not validated during the assignment in the pool configuration function. Hence there is every chance that the `pool.daoTax` to be `> 1`.
Hence this will underflow the `share - daoShare` calculation.

Hence it is recommended to conduct the `pool.daoTax < 1` check before the `daoShare` value calculation, so the `share - daoShare` will not underflow.
If the `pool.daoTax > 1` then the function should revert, since the tax charged, can not be higher than the total reward amount earned.		

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1387-L1393

## 7. IT IS RECOMMENDED TO FOLLOW THE CHECKS EFFECTS AND INTERACTIONS `CEI` ORDER IN FUNCTIONS TO ELIMINATE THE DANGER OF REENTRANCY ATTACKS.

In the `_withdrawS1Citizen`, function of the `NeoTokyoStaker` contract, the `CEI` order is not followed when performing transactions related to fund transfer.
Even though there is no threat here of reentrancy due to `reentrancyGuard` in the calling function, it is still recommended to follow the `CEI` as a best practice for this function.

so the fund transfer should be performed once all the state variables have been updated accordingly.

But as shown below the stakedBytes value is updated once the asset transfer is performed and it is not the best practice to follow.

		if (stakedCitizen.stakedBytes > 0) {
			_assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
		}
		
		stakedCitizen.stakedBytes = 0;
		
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1478
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1482-L1488
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1492
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1553
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1557
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1618

## 8. USE `delete` INSTEAD OF RESETTING THE VALUE TO `0`

It is recommended to `delete` the variable in solidity rather than resetting the value to `0`.
Because `delete` is the safe option in resetting the variable to `0` value rather than setting it to `0` manually.

		stakedCitizen.stakedBytes = 0;
		stakedCitizen.timelockEndTime = 0;
		stakedCitizen.points = 0;
		stakedCitizen.hasVault = false;
		stakedCitizen.stakedVaultId = 0;
		
above code snippet can be updated as follows:

		delete stakedCitizen.stakedBytes;
		delete stakedCitizen.timelockEndTime;
		delete stakedCitizen.points;
		delete stakedCitizen.hasVault;
		delete stakedCitizen.stakedVaultId;
		
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517-L1521
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1582-L1584

## 9. NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

	pragma solidity ^0.8.19;

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2

## 10. USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

Can import the required specific contracts, interfaces, functions or variables by using the named imports explicitly. Plain imports will import the entire context of the imported contract which could lead into variable name conflicts etc ...

Currently the `IByteContract` is imported as follows:

	import "../interfaces/IByteContract.sol";

But it can be imported explicitly by the name as follows:

	import {IByteContract} from "../interfaces/IByteContract.sol";

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L6-L8
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L7-L9

## 11. FOR IMPROVED CODE READABILITY, WHEN PERFORMING VARIABLE DECLARATIONS, USE UPPERCASE LETTERS FOR CONTSTANTS AND IMMUTABLE VARIABLES ONLY.

In the following code line a state variable is declared with uppercase letters. Even though this does not impact the functioning of the protocol in anyway, It is recommended to use lowercase letters for normal state variable declarations.
And only use uppercase letters for `constants` and `immutable` variable declarations for improved code readability of both developers and auditors.

	address public LP;

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L232

## 12. CONSTRUCTOR LACKS `address(0)` CHECKS

Zero-address check should be used in the constructors, to avoid the risk of setting protocol critical parameters to `address(0)` during contract deployment time.

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
		VAULT_CAP = _vaultCap;
		NO_VAULT_CAP = _noVaultCap;
	}
		
It is recommended to check for `address(0)` for each of the above address assignments done in the `NeoTokyoStaker` `constructor` as a precaution.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L606
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-L85

## 13. CHECK FOR THE CONTRACT EXISTENCE USING `extcodesize` IF USING LOW LEVEL `call` FUNCTION TO SEND TOKENS TO ANOTHER ACCOUNT

		(bool success, bytes memory data) = 
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_FROM_SELECTOR,
					_from,
					_to, 
					_idOrAmount
				)
			);

In the above code the `_asset` address is a contract, if it does not include any code in it, it will still return `success = true`.
Hence it is best practice to check for the contract existence of `_asset` before calling function of the contract using low level `call()` function

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L772-L780
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L801-L808

## 14. `stakedS1[msg.sender][citizenId]` IS READ FROM BUT NOT SET IN THE `NeoTokyoStaker` CONTRACT.

The StakedS1Citizen `structs` are read from the `stakedS1[msg.sender][citizenId]` `mapping`. But the mapping is never set inside the contract.

		StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
		StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
		StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
		StakedS1Citizen storage stakedCitizen = stakedS1[msg.sender][citizenId];

As shown above in multiple occassions the `stakedS1[msg.sender][citizenId]` is read from but never set inside the contract. same applies with `stakedS2[_staker][citizenId]` mapping as well.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L719
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L900
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1061		
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1281