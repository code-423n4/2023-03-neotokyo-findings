## Gas Optimizations
### Issue
## [G-01] Avoid using state variable in emit
## [G-02] Gas savings can be achieved by changing the model for assigning value to the structure
## [G-03] Use nested if and, avoid multiple check combinations
## [G-04] Use assembly to write address storage values
## [G-05] Setting the constructor to payable
## [G-06] Upgrade Solidity’s optimizer
## [G-07] Optimize names to save gas
## [G-08] Using delete instead of setting stakedCitizen struct to 0 saves gas
## [G-09] Empty blocks should be removed or emit something
## [G-10] Use constants instead of type(uintx).max
## [G-11] x += y (x -= y) costs more gas than x = x + y (x = x - y) for state variables
### Total: 58 contexts over 11 issues
## [G-01] Avoid using state variable in emit
### Context: 01
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
Using a state variable in Claim emits wastes gas.

```
		emit Claim (
			_recipient,
			totalReward,
			totalTax
		);
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1446-L1450

## [G-02] Gas savings can be achieved by changing the model for assigning value to the structure
### Description:
By changing the pattern of assigning value to the structure, gas savings of ~130 per instance are achieved. In addition, this use will provide significant savings in distribution costs.
### Context: 02
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
			stakedS1Details[i] = StakedS1CitizenOutput({
				citizenId: citizenId,
				stakedBytes: citizenDetails.stakedBytes,
				timelockEndTime: citizenDetails.timelockEndTime,
				points: citizenDetails.points,
				hasVault: citizenDetails.hasVault,
				stakedVaultId: citizenDetails.stakedVaultId
			});
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L720-L727
```
			stakedS2Details[i] = StakedS2CitizenOutput({
				citizenId: citizenId,
				stakedBytes: citizenDetails.stakedBytes,
				timelockEndTime: citizenDetails.timelockEndTime,
				points: citizenDetails.points
			});
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L737-L742
#### Recommendation:
The following model, which is more gas efficient, can be preferred to assign value to the building elements.
Ex:
```
+ stakedS1Details[i] .citizenId = citizenId
+ ...
```

## [G-03] Use nested if and, avoid multiple check combinations
### Context: 04
### Description:
Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
910:		if (citizenVaultId != 0 && vaultId != 0) {
917:		} else if (citizenVaultId != 0 && vaultId == 0) {
926:		} else if (citizenVaultId == 0 && vaultId != 0) {
1834:				if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
```

## [G-04] Use assembly to write address storage values
### Context: 10
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol
```
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
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L75-L84

#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
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
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588-L603

## [G-05] Setting the constructor to payable
### Context: 02
#### Description:
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks.
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol
```
75:	constructor (
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L75

#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
588:	constructor (
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588

## [G-06] Upgrade Solidity’s optimizer
### Context: 02
#### Description:
Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.
Set the optimization value higher than 800 in your hardhat.config.js file.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/hardhat.config.js
```
33:						runs: 200, 
45:						runs: 200, 
```

## [G-07] Optimize names to save gas
#### Description:
Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas.
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## [G-08] Using delete instead of setting stakedCitizen struct to 0 saves gas
### Contexts: 07
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
1517:		stakedCitizen.stakedBytes = 0;
1518:		stakedCitizen.timelockEndTime = 0;
1519:		stakedCitizen.points = 0;
1521:		stakedCitizen.stakedVaultId = 0;
1582:		stakedCitizen.stakedBytes = 0;
1583:		stakedCitizen.timelockEndTime = 0;
1584:		stakedCitizen.points = 0;
```

## [G-09] Empty blocks should be removed or emit something
### Contexts: 02
#### Description:
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.
#### File:  https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol
```
189:	function updateReward (
204:	function updateRewardOnMint (
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L189-L194
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L204-L209

## [G-10] Use constants instead of type(uintx).max
### Contexts: 06
#### Description:
type(uint128).max or type(uint256).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
963:		uint256 timelockMultiplier = _timelock & type(uint128).max;
1017:		uint256 timelockMultiplier = _timelock & type(uint128).max;
1141:		uint256 timelockMultiplier = _timelock & type(uint128).max;
1206:			revert InvalidAssetType(uint256(_assetType));
1301:				revert InvalidAssetType(uint256(_assetType));
1669:			revert InvalidAssetType(uint256(_assetType));
```

## [G-11] x += y (x -= y) costs more gas than x = x + y (x = x - y) for state variables
### Contexts: 22
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
977:			pool.totalPoints += citizenStatus.points;
1029:			pool.totalPoints += citizenStatus.points;
1078:				citizenStatus.stakedBytes += amount;
1079:				citizenStatus.points += bonusPoints;
1080:				pool.totalPoints += bonusPoints;
1099:				citizenStatus.stakedBytes += amount;
1100:				citizenStatus.points += bonusPoints;
1101:				pool.totalPoints += bonusPoints;
1160:			stakerLPPosition[msg.sender].amount += amount;
1161:			stakerLPPosition[msg.sender].points += points;
1164:			pool.totalPoints += points;
1283:						points += s1Citizen.points;
1292:						points += s2Citizen.points;
1298:                 points += stakerLPPosition[_recipient].points;
1332:		totalReward += currentRewardRate * timeSinceReward;	
1343:		totalReward += currentRewardRate * timeSinceReward;
1357:                           totalReward += currentRewardRate * timeSinceReward;
1515:			pool.totalPoints -= stakedCitizen.points;
1580:			pool.totalPoints -= stakedCitizen.points;
1626:			lpPosition.amount -= amount;
1627:			lpPosition.points -= points;
1620:			pool.totalPoints -= points;
```