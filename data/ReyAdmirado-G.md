| | issue |
| ----------- | ----------- |
| 1 | [state variables can be packed into fewer storage slots](#1-state-variables-can-be-packed-into-fewer-storage-slots) |
| 2 | [expressions for constant values such as a call to keccak256(), should use immutable rather than constant](#2-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant) |
| 3 | [Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate](#3-multiple-addressid-mappings-can-be-combined-into-a-single-mapping-of-an-addressid-to-a-struct-where-appropriate) |
| 4 | [state variables should be cached in stack variables rather than re-reading them from storage](#4-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) |
| 5 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#5-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 6 | [can make the variable outside the loop to save gas](#6-can-make-the-variable-outside-the-loop-to-save-gas) |
| 7 | [using `calldata` instead of `memory` for read-only arguments in external functions saves gas](#7-using-calldata-instead-of-memory-for-read-only-arguments-in-external-functions-saves-gas) |
| 8 | [ Ternary over if ... else](#8-ternary-over-if--else) |
| 9 | [before some functions we should check some variables for possible gas save](#9-before-some-functions-we-should-check-some-variables-for-possible-gas-save) |
| 10 | [instead of calculating a statevar with keccak256() every time the contract is made pre calculate them before and only give the result to a constant](#10-instead-of-calculating-a-statevar-with-keccak256-every-time-the-contract-is-made-pre-calculate-them-before-and-only-give-the-result-to-a-constant) |
| 11 | [Optimize names to save gas](#11-optimize-names-to-save-gas) |
| 12 | [Non-strict inequalities are cheaper than strict ones](#12-non-strict-inequalities-are-cheaper-than-strict-ones) |
| 13 | [Setting the constructor to payable](#13-setting-the-constructor-to-payable) |
| 14 | [instead of assigning to zero use `delete`](#14-instead-of-assigning-to-zero-use-delete) |
| 15 | [part of the code can be pre calculated](#15-part-of-the-code-can-be-pre-calculated) |


## 1. state variables can be packed into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper.

`lpLocked`(#L511) and `LP`(#232) can be put together in the same slot if they be declared near each other. they are accessed in the same function only once so mainly saves Gsset of 20000 for a slot and near 40 gas.
- [NeoTokyoStaker.sol#L232](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L232)


## 2. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [BYTES2.sol#L37](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37)
- [BYTES2.sol#L40](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L40)

- [NeoTokyoStaker.sol#L206](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206)
- [NeoTokyoStaker.sol#L209](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L209)
- [NeoTokyoStaker.sol#L214](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L214)
- [NeoTokyoStaker.sol#L217](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L217)
- [NeoTokyoStaker.sol#L220](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L220)


## 3. Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. 

`stakedS1`(#L372) and `stakedS2`(#L405) and `stakerLPPosition`(#L434) can be put together to save lots of gas because they are used in the same function multiple times
- [NeoTokyoStaker.sol#L372](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L372)


`_stakerS1Position`(#L378) and `_stakerS2Position`(#L411) can be put together to save lots of gas because they are used in the same function multiple times
- [NeoTokyoStaker.sol#L378](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L378)


## 4. state variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 

`_stakerS1Position[_staker].length` can be cached before #L715 because its used twice (in #L716 and #L717) and the cached version can be used instead
- [NeoTokyoStaker.sol#L716](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L716)

`_stakerS2Position[_staker].length` can be cached before #L732 because its used twice (in #L733 and #L734) and the cached version can be used instead
- [NeoTokyoStaker.sol#L733](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L733)

`stakerLPPosition[msg.sender].multiplier` will be checked in #L1144 and if it fails we gonna have another complex SLOAD so we can cache it before the first check and risk losing 3 gas if the first check happens to be true otherwise we reduce a complex SLOAD. cache it before #L1144
- [NeoTokyoStaker.sol#L1144](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1144)

cache `LP` because its being read twice (in #L1137 and #L1170). so cache it before #L1137 to save 97 gas.
- [NeoTokyoStaker.sol#L1137](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1137)

`stakedCitizen.timelockEndTime` is being read twice and should be cached to save gas. first SLOAD is in #L1467 and the second one depending on if the #L1467 condition be true or false will be #L1467 or #L1472. so cache it before #L1467
- [NeoTokyoStaker.sol#L1467](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1467)

possibly reduce a complex SLOAD by risking losing 3 gas. caching `stakedCitizen.stakedBytes` will save gas if the condition happens and if it doesnt we only lose 3 gas for caching. cache before #L1477
- [NeoTokyoStaker.sol#L1477](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1477)

possibly reduce a complex SLOAD by risking losing 3 gas. caching `stakedCitizen.stakedVaultId` will save gas if the condition happens and if it doesnt we only lose 3 gas for caching. cache before #L1482
- [NeoTokyoStaker.sol#L1482](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1482)

cache `LP` because its being read twice (in #L1618 and #L1641). so cache it before #L1618 to save 97 gas.
- [NeoTokyoStaker.sol#L1618](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1618)


## 5. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas (13 or 113 each dependant on the usage see [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8))

- [NeoTokyoStaker.sol#L977](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L977)
- [NeoTokyoStaker.sol#L1029](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1029)
- [NeoTokyoStaker.sol#L1078](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1078)
- [NeoTokyoStaker.sol#L1079](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1079)
- [NeoTokyoStaker.sol#L1080](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1080)
- [NeoTokyoStaker.sol#L1099](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1099)
- [NeoTokyoStaker.sol#L1100](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1100)
- [NeoTokyoStaker.sol#L1101](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1101)
- [NeoTokyoStaker.sol#L1160](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1160)
- [NeoTokyoStaker.sol#L1161](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1161)
- [NeoTokyoStaker.sol#L1164](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1164)
- [NeoTokyoStaker.sol#L1515](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1515)
- [NeoTokyoStaker.sol#L1580](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1580)
- [NeoTokyoStaker.sol#L1626](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1626)
- [NeoTokyoStaker.sol#L1627](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1627)
- [NeoTokyoStaker.sol#L1630](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1630)


## 6. can make the variable outside the loop to save gas

make the variable outside and only give the value to variable inside

- [NeoTokyoStaker.sol#L735-L736](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L735-L736)
- [NeoTokyoStaker.sol#L1280-L1281](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L)
- [NeoTokyoStaker.sol#L1289-L1290](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1289-L1290)
- [NeoTokyoStaker.sol#L1313](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1313)
- [NeoTokyoStaker.sol#L1823](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1823)


## 7. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. 

- [NeoTokyoStaker.sol#L1739-L1740](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1739-L1740)
- [NeoTokyoStaker.sol#L1761-L1763](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1761-L1763)
- [NeoTokyoStaker.sol#L1784-L1785](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1784-L1785)
- [NeoTokyoStaker.sol#L1803-L1804](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1803-L1804)
- [NeoTokyoStaker.sol#L1820](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1820)


## 8. Ternary over if ... else

Using ternary operator instead of the if else statement saves gas.

- [NeoTokyoStaker.sol#L950-L954](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L950-L954)
- [NeoTokyoStaker.sol#L1354-L1367](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1354-L1367)


## 9. before some functions we should check some variables for possible gas save

before transfer we should check for amount being 0 so the function doesnt run when its not gonna do anything

`amount`
- [NeoTokyoStaker.sol#L1057](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1057)
- [NeoTokyoStaker.sol#L1377](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1377)
- [NeoTokyoStaker.sol#L1618](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1618)


## 10. instead of calculating a statevar with keccak256() every time the contract is made pre calculate them before and only give the result to a constant

- [BYTES2.sol#L37](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37)
- [BYTES2.sol#L40](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L40)

- [NeoTokyoStaker.sol#L206](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206)
- [NeoTokyoStaker.sol#L209](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L209)
- [NeoTokyoStaker.sol#L214](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L214)
- [NeoTokyoStaker.sol#L217](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L217)
- [NeoTokyoStaker.sol#L220](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L220)


## 11. Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.

See more [here](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).
you can use this [tool](https://emn178.github.io/solidity-optimize-name/) to get the optimized version for function and properties signitures 


## 12. Non-strict inequalities are cheaper than strict ones

In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).
consider replacing >= with the strict counterpart > and add `- 1` to the second side

- [NeoTokyoStaker.sol#L1215](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1215)
- [NeoTokyoStaker.sol#L1329](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1329)
- [NeoTokyoStaker.sol#L1834](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1834)


## 13. Setting the constructor to payable

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks.

- [BYTES2.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol)
- [NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)


## 14. instead of assigning to zero use `delete` 

assigning to zero uses more gas than using `delete` , and they both assign variable to default value. so it is encouraged if the data is no longer needed use delete instead.

5 instances here
- [NeoTokyoStaker.sol#L1517-L1521](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517-L1521)

3 instances here
- [NeoTokyoStaker.sol#L1582-L1584](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1582-L1584)

1 here
- [NeoTokyoStaker.sol#L1635](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1635)


## 15. part of the code can be pre calculated

just pre calculate the answer of these parts and use the answer inside the code instead. use a comment to show what it was to the readers

`_DIVISOR / _DIVISOR` can be pre calculated and given to the contract as a constant. this stops one operation
- [NeoTokyoStaker.sol#L970](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L970)

`100 * timelockMultiplier / _DIVISOR` can just use `timelockMultiplier` because the other operations result will be 1
- [NeoTokyoStaker.sol#L1022](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1022)

`100 / _BYTES_PER_POINT` can be pre calculated and given to the contract as a constant. this stops one operation
- [NeoTokyoStaker.sol#L1077](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1077)
- [NeoTokyoStaker.sol#L1098](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1098)

`amount * 100 / 1e18 * timelockMultiplier / _DIVISOR` we can pre calculate the `100 / 1e18 / _DIVISOR` part to reduce use of 2 operations
- [NeoTokyoStaker.sol#L1155](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155)

`100 * _DIVISOR` can be pre calculated and given to the contract as a constant. this stops one operation
- [NeoTokyoStaker.sol#L1389](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1389)

`amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR` we can pre calculate the `100 / 1e18 / _DIVISOR` part to reduce use of 2 operations
- [NeoTokyoStaker.sol#L1623](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1623)
