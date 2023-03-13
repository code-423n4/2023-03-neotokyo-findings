# Gas Optimizations Summary
| |Issue|Instances|
|:-:|:-|:-:|
|[G-01]|Use `calldata` instead of `memory` for function arguments that are read only|12|
|[G-02]|Duplicated `require`/`revert` checks should be refactored to a modifier or function.|2|
|[G-03]|Use `indexed` to save gas|6|
|[G-04]|Cache length outside of for loop|11|
|[G-05]|Expressions for constant values such as a call to `keccak256` should use `immutable` rather than `constant`|7|
|[G-06]|Use `unchecked` for operations that cannot overflow/underflow|4|
|[G-07]|`++i` costs less gas than `i++`|9|
|[G-08]|`_stringEquals` can be optimized|1|

Total issues: 8

Total instances: 52

&nbsp;
# Gas Optimizations
## [G-01] Use `calldata` instead of `memory` for function arguments that are read only

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * `<mem_array>.length`). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

If the array is passed to an `internal` function which passes the array to another `internal` function where the array is modified and therefore `memory` is used in the `external` call, it's still more gas-efficient to use `calldata` when the external function uses modifiers, since the modifiers may prevent the `internal` functions from being called. `Structs` have the same overhead as an array of length one

Instances: 12
- [contracts/staking/NeoTokyoStaker.sol#L825](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L825)
- [contracts/staking/NeoTokyoStaker.sol#L826](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L826)
- [contracts/staking/NeoTokyoStaker.sol#L1739](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1739)
- [contracts/staking/NeoTokyoStaker.sol#L1740](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1740)
- [contracts/staking/NeoTokyoStaker.sol#L1761](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1761)
- [contracts/staking/NeoTokyoStaker.sol#L1762](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1762)
- [contracts/staking/NeoTokyoStaker.sol#L1763](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1763)
- [contracts/staking/NeoTokyoStaker.sol#L1784](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1784)
- [contracts/staking/NeoTokyoStaker.sol#L1785](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1785)
- [contracts/staking/NeoTokyoStaker.sol#L1803](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1803)
- [contracts/staking/NeoTokyoStaker.sol#L1804](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1804)
- [contracts/staking/NeoTokyoStaker.sol#L1820](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1820)

&nbsp;
## [G-02] Duplicated `require`/`revert` checks should be refactored to a modifier or function.

Instances: 2
- [contracts/staking/NeoTokyoStaker.sol#L784](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L784)
- [contracts/staking/NeoTokyoStaker.sol#L812](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L812)

&nbsp;
## [G-03] Use `indexed` to save gas
Using `indexed` for value type event parameters (address, uint, bool) saves at least 80 gas in emitting the event (https://gist.github.com/Tomosuke0930/9bf61e01a8c3e214d95a9b84dcb41d97).

Note that for other types however (string, bytes), it is more expensive.

Instances: 6
- [contracts/staking/BYTES2.sol#L63](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L63)
- [contracts/staking/NeoTokyoStaker.sol#L542](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L542)
- [contracts/staking/NeoTokyoStaker.sol#L543](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L543)
- [contracts/staking/NeoTokyoStaker.sol#L555](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L555)
- [contracts/staking/NeoTokyoStaker.sol#L556](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L556)
- [contracts/staking/NeoTokyoStaker.sol#L570](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L570)

&nbsp;
## [G-04] Cache length outside of for loop

Currently, the solidity compiler will always read the length of the array during each iteration.

That is, if it is a storage array, thisis an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

Instances: 11
- [contracts/staking/NeoTokyoStaker.sol#L717](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L717)
- [contracts/staking/NeoTokyoStaker.sol#L734](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L734)
- [contracts/staking/NeoTokyoStaker.sol#L1279](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1279)
- [contracts/staking/NeoTokyoStaker.sol#L1288](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1288)
- [contracts/staking/NeoTokyoStaker.sol#L1499](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1499)
- [contracts/staking/NeoTokyoStaker.sol#L1564](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1564)
- [contracts/staking/NeoTokyoStaker.sol#L1742](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1742)
- [contracts/staking/NeoTokyoStaker.sol#L1765](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1765)
- [contracts/staking/NeoTokyoStaker.sol#L1787](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1787)
- [contracts/staking/NeoTokyoStaker.sol#L1806](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1806)
- [contracts/staking/NeoTokyoStaker.sol#L1822](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1822)

&nbsp;
## [G-05] Expressions for constant values such as a call to `keccak256` should use `immutable` rather than `constant`

See [here](https://github.com/ethereum/solidity/issues/9232) for a detailed description of the issue.

Instances: 7
- [contracts/staking/BYTES2.sol#L37](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37)
- [contracts/staking/BYTES2.sol#L40](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L40)
- [contracts/staking/NeoTokyoStaker.sol#L206](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206)
- [contracts/staking/NeoTokyoStaker.sol#L209](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L209)
- [contracts/staking/NeoTokyoStaker.sol#L214](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L214)
- [contracts/staking/NeoTokyoStaker.sol#L217](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L217)
- [contracts/staking/NeoTokyoStaker.sol#L220](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L220)

&nbsp;
## [G-06] Use `unchecked` for operations that cannot overflow/underflow

By bypassing Solidity's built in overflow/underflow checks using `unchecked`, we can save gas. This is especially beneficial for the index variable within for loops (saves 120 gas per iteration).

Instances: 4
- [contracts/staking/NeoTokyoStaker.sol#L1283](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1283)
- [contracts/staking/NeoTokyoStaker.sol#L1292](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1292)
- [contracts/staking/NeoTokyoStaker.sol#L1298](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1298)
- [contracts/staking/NeoTokyoStaker.sol#L1442](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1442)

&nbsp;
## [G-07] `++i` costs less gas than `i++`

True for `--i`/`i--` as well, and is especially important in for loops. Saves 5 gas per iteration.

Instances: 9
- [contracts/staking/NeoTokyoStaker.sol#L728](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L728)
- [contracts/staking/NeoTokyoStaker.sol#L743](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L743)
- [contracts/staking/NeoTokyoStaker.sol#L1284](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1284)
- [contracts/staking/NeoTokyoStaker.sol#L1293](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1293)
- [contracts/staking/NeoTokyoStaker.sol#L1369](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1369)
- [contracts/staking/NeoTokyoStaker.sol#L1383](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1383)
- [contracts/staking/NeoTokyoStaker.sol#L1509](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1509)
- [contracts/staking/NeoTokyoStaker.sol#L1574](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1574)
- [contracts/staking/NeoTokyoStaker.sol#L1838](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1838)

&nbsp;
## [G-08] `_stringEquals` can be optimized

When checking for equality of two strings, one can use `keccak256` as follows:

```solidity
function _stringEquals(string memory _a, string memory _b) public pure returns (bool) {
    return keccak256(bytes(_a)) == keccak256(bytes(_b));
}
```

This saves ~20,000 gas on deployment when the original lengthy `_stringEquals` function is replaced, and ~150 gas per call. `_stringEquals` appears in `_stakeS1Citizen` which is in scope.

Instances: 1
- [contracts/staking/NeoTokyoStaker.sol#L824-865](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L824-L865)
