# Summary

## Gas Optimizations

|        | Issue                                                                                                                                               |
| ------ | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| GAS-1  | `<X> += <Y>`/`<X> -= <Y>` COSTS MORE GAS THAN `<X> = <X> + <Y>`/`<X> = <X> - <Y>` FOR STATE VARIABLES                                               |
| GAS-2  | SETTING THE CONSTRUCTOR TO PAYABLE                                                                                                                  |
| GAS-3  | USE CUSTOM ERRORS                                                                                                                                   |
| GAS-4  | DUPLICATED REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION                                                                           |
| GAS-5  | DOS WITH BLOCK GAS LIMIT                                                                                                                            |
| GAS-6  | INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT |
| GAS-7  | CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT                                                            |
| GAS-8  | KECCAK256() SHOULD ONLY NEED TO BE CALLED ON A SPECIFIC STRING LITERAL ONCE                                                                         |
| GAS-9  | MAKING CONSTANT VARIABLES PRIVATE WILL SAVE GAS DURING DEPLOYMENT                                                                                   |
| GAS-10 | OPTIMIZE NAMES TO SAVE GAS                                                                                                                          |
| GAS-11 | USAGE OF `UINT`/`INT` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD                                                                              |
| GAS-12 | UNNECESSARY LIBRARIES                                                                                                                               |

### [GAS-1] `<X> += <Y>`/`<X> -= <Y>` COSTS MORE GAS THAN `<X> = <X> + <Y>`/`<X> = <X> - <Y>` FOR STATE VARIABLES

#### Description:

Using the addition operator instead of plus-equals saves gas

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

977:             pool.totalPoints += citizenStatus.points;

1029:             pool.totalPoints += citizenStatus.points;

1078:                 citizenStatus.stakedBytes += amount;

1079:                 citizenStatus.points += bonusPoints;

1080:                 pool.totalPoints += bonusPoints;

1099:                 citizenStatus.stakedBytes += amount;

1100:                 citizenStatus.points += bonusPoints;

1101:                 pool.totalPoints += bonusPoints;

1160:             stakerLPPosition[msg.sender].amount += amount;

1161:             stakerLPPosition[msg.sender].points += points;

1164:             pool.totalPoints += points;

1283:                         points += s1Citizen.points;

1292:                         points += s2Citizen.points;

1298:                     points += stakerLPPosition[_recipient].points;

1332:             totalReward += currentRewardRate * timeSinceReward;

1343:             totalReward += currentRewardRate * timeSinceReward;

1357:             totalReward += currentRewardRate * timeSinceReward;

1515:             pool.totalPoints -= stakedCitizen.points;

1580:             pool.totalPoints -= stakedCitizen.points;

1626:             lpPosition.amount -= amount;

1627:             lpPosition.points -= points;

1630:             pool.totalPoints -= points;

```

### [GAS-2] SETTING THE CONSTRUCTOR TO PAYABLE

#### Description:

Saves ~13 gas per instance

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

75:     constructor(

```

```solidity
File: NeoTokyoStaker.sol

588:     constructor(

```

### [GAS-3] USE CUSTOM ERRORS

#### Description:

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

Reference: https://blog.soliditylang.org/2021/04/21/custom-errors/

Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

Reference: https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

784:             revert(string(data));

812:             revert(string(data));

```

### [GAS-4] DUPLICATED REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

#### Description:

The compiler will inline the function, which will avoid JUMP instructions usually associated with functions

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

784:             revert(string(data));

812:             revert(string(data));

```

### [GAS-5] DOS WITH BLOCK GAS LIMIT

#### Description:

When smart contracts are deployed or functions inside them are called, the execution of these actions always requires a certain amount of gas, based of how much computation is needed to complete them. The Ethereum network specifies a block gas limit and the sum of all transactions included in a block can not exceed the threshold.

Programming patterns that are harmless in centralized applications can lead to Denial of Service conditions in smart contracts when the cost of executing a function exceeds the block gas limit. Modifying an array of unknown size, that increases in size over time, can lead to such a Denial of Service condition.

reference: https://swcregistry.io/docs/SWC-128

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

1499:         for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {

1564:         for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {

```

#### Recommended Mitigation Steps:

Caution is advised when you expect to have large arrays that grow over time. Actions that require looping across the entire data structure should be avoided.

If you absolutely must loop over an array of unknown size, then you should plan for it to potentially take multiple blocks, and therefore require multiple transactions.

### [GAS-6] INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

37:     bytes32 public constant BURN = keccak256("BURN");

40:     bytes32 public constant ADMIN = keccak256("ADMIN");

```

```solidity
File: NeoTokyoStaker.sol

206:     bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

209:     bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);

214:     bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

217:     bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

220:     bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```

### [GAS-7] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

37:     bytes32 public constant BURN = keccak256("BURN");

40:     bytes32 public constant ADMIN = keccak256("ADMIN");

```

```solidity
File: NeoTokyoStaker.sol

206:     bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

209:     bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);

214:     bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

217:     bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

220:     bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```

### [GAS-8] KECCAK256() SHOULD ONLY NEED TO BE CALLED ON A SPECIFIC STRING LITERAL ONCE

#### Description:

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once.

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

37:     bytes32 public constant BURN = keccak256("BURN");

40:     bytes32 public constant ADMIN = keccak256("ADMIN");

```

```solidity
File: NeoTokyoStaker.sol

206:     bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

209:     bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);

214:     bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

217:     bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

220:     bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```

### [GAS-9] MAKING CONSTANT VARIABLES PRIVATE WILL SAVE GAS DURING DEPLOYMENT

#### Description:

When constants are marked public, extra getter functions are created, increasing the deployment cost. Marking these functions private will decrease gas cost. One can still read these variables through the source code. If they need to be accessed by an external contract, a separate single getter function can be used to return all constants as a tuple. There are four instances of public constants.

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

37:     bytes32 public constant BURN = keccak256("BURN");

40:     bytes32 public constant ADMIN = keccak256("ADMIN");

```

```solidity
File: NeoTokyoStaker.sol

206:     bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

209:     bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);

214:     bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

217:     bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

220:     bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```

### [GAS-10] OPTIMIZE NAMES TO SAVE GAS

#### Description:

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. In this report are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).

[Solidity optimize name github](https://github.com/enzosv/solidity-optimize-name)

[Source](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

34: contract BYTES2 is PermitControl, ERC20("BYTES", "BYTES") {

```

```solidity
File: NeoTokyoStaker.sol

188: contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

```

### [GAS-11] USAGE OF `UINT`/`INT` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

#### Description:

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

1205:         if (uint8(_assetType) > 4) {

1668:         if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {

```

### [GAS-12] UNNECESSARY LIBRARIES

#### Description:

Libraries are often only imported for a small number of uses, meaning that they can contain a significant amount of code that is redundant to your contract. If you can safely and effectively implement the functionality imported from a library within your contract, it is optimal to do so.

Reference: https://betterprogramming.pub/how-to-write-smart-contracts-that-optimize-gas-spent-on-ethereum-30b5e9c5db85

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```
