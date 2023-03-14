### Issues 
| Letter | Name | Description |
|:--:|:-------:|:-------:|
| L  | Low risk | Potential risk |
| NC |  Non-critical | Non risky findings |
| R  | Refactor | Changing the code |
| O | Ordinary | Often found issues |
| S | Suggestions | Suggestion Details |

| Total Found Issues | 23 |
|:--:|:--:|

### Non-Critical
| Count | Explanation | Instances |
|:--:|:-------|:--:|
| [N-01] | CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD BE USED AS IMMUTABLE RATHER THAN CONSTANT | 7 |
| [N-02] | SORT SOLIDITY OPERATIONS USING SHORT-CIRCUIT MODE | 7 |
| [N-03] | FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE | ALL CONTRACTS |
| [N-04] | PRIVATE FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS  |  |
| [N-05] | TAKE ADVANTAGE OF CUSTOM ERROR'S RETURN VALUE PROPERTY | 4 |
| [N-06] | LACK OF ADDRESS(0) CHECKS IN THE CONSTRUCTOR | 2 |
| [N-07] | ASSEMBLY CODES SPECIFIC - SHOULD HAVE COMMENTS | 8 |
| [N-08] | CREATE YOUR OWN IMPORT NAMES INSTEAD OF USING THE REGULAR ONES | All CONTRACTS |
| [N-09] | EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING | 2 |
| [N-10] | NATSPEC DONT COMPLY WITH SOLDITY STYLE GUIDE | ALL CONTRACTS |
| [N-11] | USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS | ALL CONTRACTS |
| [N-12] | MISSING EMERGENCY STOP (CIRCUIT BREAKER) PATTERN | 2 |
| [N-13] | NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES | all contracts |
| [N-14] | NEED FUZZING TEST | 29 |

| Total Non-Critical Issues | 14 | Total Instances | 61 |
|:--:|:--:|:--:|--:|

### Refactor Issues 
| Count | Explanation | Instances |
|:--:|:-------|:--:|
| [R-01] | SHORTHAND WAY TO WRITE IF/ELSE STATEMENT | 5 |
| [R-02] | USING CALLDATA INSTEAD OF MEMORY | 5 |
| [R-03] | CACHE THE MAPPING VALUES RATHER THAN FETCH IT EVERY TIME | 2 |
| [R-04] | USING DELETE INSTEAD OF SETTING STRUCT 0 SAVES GAS | 8 |
| [R-05] | USE THE LATEST UPDATED VERSION OF OPENZEPPELIN DEPENDENCIES | 2 |
| [R-06] | VARIABLE NAMES THAT CONSIST OF ALL CAPITAL LETTERS SHOULD BE RESERVED FOR CONST /IMMUTABLE VARIABLES | 5 |

| Total Refactor Issues | 6 | Total Instances | 27 |
|:--:|:--:|:--:|--:|

### Ordinary Issues
| Count | Explanation | Instances |
|:--:|:-------|:--:|
| [O-01] | USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS | 7 |
| [O-02] | <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES (-= TOO) | 17 |

| Total Ordinary Issues | 2 | Total Instances | 24 |
|:--:|:--:|:--:|--:|

### Suggestion Details 
| Count | Explanation | 
|:--:|:-------|
| [S-01] | GENERATE PERFECT CODE HEADERS EVERY TIME  |  |

| Total Suggestions | 1 |
|:--:|:--:|

# Detailed Findings
## [N-01] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD BE USED AS IMMUTABLE RATHER THAN CONSTANT
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

### PROOF OF CONCEPT
```solidity
contracts/staking/NeoTokyoStaker.sol
206: bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
209: bytes32 public constant CONFIGURE_TIMELOCKS = keccak256("CONFIGURE_TIMELOCKS");
214: bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
217: bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
220: bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
	

contracts/staking/BYTES2.sol
37:	bytes32 public constant BURN = keccak256("BURN");
40:	bytes32 public constant ADMIN = keccak256("ADMIN");
```

## [N-02] SORT SOLIDITY OPERATIONS USING SHORT-CIRCUIT MODE
Short-circuiting is a solidity contract development model that uses `OR/AND` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```solidity
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```
### PROOF OF CONCEPT
- [ Issue in NeoTokyoStaker ](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L910-L930)
- [ Issue in NeoTokyoStaker ](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1834)
- [ Issue in NeoTokyoStaker ](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668)


## [N-03] FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE
According to the Solidity style guide, functions should be laid out in the following order: 

- constructor() 
- receive() 
- fallback() 
- external
- public 
- internal 
- private

[Solidity Order Functions](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions)

Functions should be grouped according to their visibility and ordered: `within a grouping, place the view and pure functions last`

## [N-04] PRIVATE FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Inlining optimization is a technique that can improve code efficiency and save gas. When a function is called in Solidity, a JUMP to the function's location occurs, followed by the execution of the function's instructions, and then another JUMP back to the point of origin. This process requires the use of additional memory and therefore costs gas.

However, if a function is called only once and has no side effects on the contract state, inlining optimization can be used to save gas. Instead of jumping to the function's location, the compiler can simply copy and paste the function's content to the place where the function is called. This eliminates the costs of additional jump instructions and additional stack operations needed for the function call.

You can see a [simple example](https://gist.github.com/catellaTech/b33b3567b71149869aa3bf280de4795f) of this.

### PROOF OF CONCEPT
```solidity
contracts/staking/NeoTokyoStaker.sol

796: function _assetTransfer 
875: function _stakeS1Citizen
```
`Multiples instances in this contract`

### MITIGATION
Consider see this [simple example](https://gist.github.com/catellaTech/b33b3567b71149869aa3bf280de4795f).

## [N-05] TAKE ADVANTAGE OF CUSTOM ERROR'S RETURN VALUE PROPERTY 
An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the `()` sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

### PROOF OF CONCEPT
```solidity
contracts/staking/NeoTokyoStaker.sol

1149: revert MismatchedTimelock();
1415: revert CallerIsNotBYTES();
1712: revert LockedConfigurationOfLP();
1835: revert RewardWindowTimesMustIncrease();
```

## [N-06] LACK OF ADDRESS(0) CHECKS IN THE CONSTRUCTOR
Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

### PROOF OF CONCEPT
```soldity
contracts/staking/NeoTokyoStaker.sol
588: constructor (
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

contracts/staking/BYTES2.sol
75: 	constructor (
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
### MITIGATION
Consider adding zero-address checks in the discussed constructors:  

```solidity
require(newAddr != address(0));
```

## [N-07] ASSEMBLY CODES SPECIFIC - SHOULD HAVE COMMENTS
Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

### PROOF OF CONCEPT
```solidity
contracts/staking/NeoTokyoStaker.sol

833:  assembly{//..}
1050: assembly{//..}
1128: assembly{//..}
1236: assembly{//..}
1461: assembly{//..}
1536: assembly{//..}
1599: assembly{//..}
1682: assembly{//..}
```

## [N-08] CREATE YOUR OWN IMPORT NAMES INSTEAD OF USING THE REGULAR ONES
For better readability, you should name the imports instead of using the regular ones.

Example:
```solidity
5: import { ExampleView } from "../libraries/ExampleView.sol";
```
`Instances - All of the contracts.`

## [N-09] EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING
In Solidity, any function defined in a non-abstract contract must have a complete implementation that specifies what to do when that function is called.

### PROOF OF CONCEPT
```solidity
contracts/staking/BYTES2.sol

189: function updateReward
204: function updateRewardOnMint
```
### MITIGATION
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

## [N-10] NATSPEC DONT COMPLY WITH SOLDITY STYLE GUIDE 
Be consistent with style and best practices in the contract.

### PROOF OF CONCEPT
The NatSpec notation requires the use of /// or /** to differentiate from regular comments.

```solidity
/*
	A new Vault to stake may only be provided if the S1 Citizen being staked 
	does not already have a component Vault.
*/
```
### MITIGATION
[Natspec Solidity Docs](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#natspec).

## [N-11] | USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS
There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

`constants.sol` Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

### PROOF OF CONCEPT
```solidity
contracts/staking/NeoTokyoStaker.sol
206: bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
209: bytes32 public constant CONFIGURE_TIMELOCKS = keccak256("CONFIGURE_TIMELOCKS");
214: bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
217: bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
220: bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
	

contracts/staking/BYTES2.sol
37:	bytes32 public constant BURN = keccak256("BURN");
40:	bytes32 public constant ADMIN = keccak256("ADMIN");
```

## [N-12] MISSING EMERGENCY STOP (CIRCUIT BREAKER) PATTERN
At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation. This can also be called an `EMERGENCY STOP (CIRCUIT BREAKER) PATTERN`. Use the follow example to implement it into the follows functions in the `NeoTokyoStaker.sol`, `stake` and `withdraw`.

[Emergency Stop Pattern Example](https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol)


## [N-13] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

### PROOF OF CONCEPT
```solidity
contracts/staking/NeoTokyoStaker.sol
2: pragma solidity ^0.8.19;

contracts/staking/BYTES2.sol
2: pragma solidity ^0.8.19;
```
## [N-14] NEED FUZZING TEST
In total `29 unchecked` are used in the contract `NeoTokyoStaker.sol`, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

### MITIGATION
Use should fuzzing test like Echidna or foundry fuzzing test.

As Alberto Cuesta Canada said: Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now.

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

## [R-01] SHORTHAND WAY TO WRITE IF/ELSE STATEMENT
The normal if / else statement can be refactored in a shorthand way to write it:
- Increases readability
- Shortens the overall SLOC.

### PROOF OF CONCEPT
```solidity
contracts/staking/NeoTokyoStaker.sol

if (_stringEquals(class, "Hand of Citadel")) {
				vaultMultiplier = vaultCreditMultiplier["?"];
			} else {
				revert CitizenIsNotHand(citizenId);
			}
```
`Multiples instances in this contract`

### MITIGATION
`Follow your own best practice like in this case`
```solidity 
string memory vaultMultiplier = (_vaultId != 0)
			? vault.getCreditMultiplier(_vaultId)
			: "";
``` 

## [R-02] USING CALLDATA INSTEAD OF MEMORY
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it’s still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one.

### PROOF OF CONCEPT
```solidity 
contracts/staking/NeoTokyoStaker.sol

1739: uint256[] memory _timelockIds
1740: uint256[] memory _encodedSettings
1761: uint256[] memory _citizenRewardRates
1785: uint256[] memory _points
1804: uint256[] memory _multipliers
```

## [R-03] CACHE THE MAPPING VALUES RATHER THAN FETCH IT EVERY TIME
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. 

### PROOF OF CONCEPT
```solidity 
contracts/staking/NeoTokyoStaker.sol

1788: identityCreditPoints[_identityCreditYields[i]] = _points[i];
1807: vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];

```
### MITIGATION
`Follow your best practice like:`
```solidity 
contracts/staking/NeoTokyoStaker.sol

1310: uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
```

## [R-04] USING DELETE INSTEAD OF SETTING STRUCT 0 SAVES GAS
### PROOF OF CONCEPT
```solidity
staking/NeoTokyoStaker.sol

1582: stakedCitizen.stakedBytes = 0;
1583: stakedCitizen.timelockEndTime = 0;
1584: stakedCitizen.points = 0;
```
- [Lines of code in NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517-L1521)

### MITIGATION
```diff
staking/NeoTokyoStaker.sol#L1517-L1521
- 1517:  stakedCitizen.stakedBytes = 0;
+ 1517:  delete stakedCitizen;
```
## [R-05] USE THE LATEST UPDATED VERSION OF OPENZEPPELIN DEPENDENCIES
### PROOF OF CONCEPT
```json
package.json

9: "@openzeppelin/contracts-upgradeable": "^4.4.2",
23: "@openzeppelin/contracts": "^4.3.1",

```
### MITIGATION
Upgrade OpenZeppelin to version [4.8.0](https://github.com/OpenZeppelin/openzeppelin-contracts/releases/)

## [R-06] VARIABLE NAMES THAT CONSIST OF ALL CAPITAL LETTERS SHOULD BE RESERVED FOR CONST /IMMUTABLE VARIABLES 
### PROOF OF CONCEPT
```solidity
contracts/staking/BYTES2.sol
49: address public STAKER;
52: address public TREASURY;

contracts/staking/NeoTokyoStaker.sol
232: address public LP;
249: uint256 public VAULT_CAP;
255: uint256 public NO_VAULT_CAP;
```

## [O-01] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declaring the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct.

### PROOF OF CONCEPT
```solidity 
contracts/staking/NeoTokyoStaker.sol

715: StakedS1CitizenOutput[] memory stakedS1Details
719: StakedS1Citizen memory citizenDetails
732: StakedS2CitizenOutput[] memory stakedS2Details
736: StakedS2Citizen memory citizenDetails
1281: StakedS1Citizen memory s1Citizen
1290: StakedS2Citizen memory s2Citizen
1313: RewardWindow memory window
```

## [O-02] <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES (-= TOO)
Using the addition operator instead of plus-equals saves [113 gas](https://gist.github.com/catellaTech/8539f7345b17f0929a41598e7e00e3c2). Subtractions act the same way.

### PROOF OF CONCEPT
```solidity 
contracts/staking/NeoTokyoStaker.sol

977: pool.totalPoints += citizenStatus.points;
1029: pool.totalPoints += citizenStatus.points;
1078: citizenStatus.stakedBytes += amount;
1079: citizenStatus.points += bonusPoints;
1080: pool.totalPoints += bonusPoints;
1099: citizenStatus.stakedBytes += amount;
1100: citizenStatus.points += bonusPoints;
1101: pool.totalPoints += bonusPoints;
1160: stakerLPPosition[msg.sender].amount += amount;
1161: stakerLPPosition[msg.sender].points += points;
1164: pool.totalPoints += points;
1284: points += s1Citizen.points;
1292: points += s2Citizen.points;
1298: points += stakerLPPosition[_recipient].points;
1332: totalReward += currentRewardRate * timeSinceReward;	
1343: totalReward += currentRewardRate * timeSinceReward;
1358: totalReward += currentRewardRate * timeSinceReward;
```

## [S-01] GENERATE PERFECT CODE HEADERS EVERY TIME
I recommend using header for Solidity code layout and readability

[This is gonna help you.](https://github.com/transmissions11/headers)


/*//////////////////////////////////////////////////////////////
                           TESTING 1
//////////////////////////////////////////////////////////////*/
