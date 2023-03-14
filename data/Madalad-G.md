# Gas Optimizations Summary
| |Issue|Instances|
|:-:|:-|:-:|
|[G-01]|Use `calldata` instead of `memory` for function arguments that are read only|12|
|[G-02]|Use `indexed` to save gas|6|
|[G-03]|Expressions for constant values such as a call to `keccak256` should use `immutable` rather than `constant`|7|
|[G-04]|Functions guaranteed to revert when called by normal users can be marked `payable`|11|
|[G-05]|`_stringEquals` can be optimized|1|

Total issues: 5

Total instances: 37

&nbsp;
# Gas Optimizations
## [G-01] Use `calldata` instead of `memory` for function arguments that are read only

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * `<mem_array>.length`). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

If the array is passed to an `internal` function which passes the array to another `internal` function where the array is modified and therefore `memory` is used in the `external` call, it's still more gas-efficient to use `calldata` when the external function uses modifiers, since the modifiers may prevent the `internal` functions from being called. `Structs` have the same overhead as an array of length one

Instances: 12
```solidity
File: contracts/staking/NeoTokyoStaker.sol

825:		string memory _a,

826:		string memory _b

1739:		uint256[] memory _timelockIds,

1740:		uint256[] memory _encodedSettings

1761:		uint256[] memory _citizenRewardRates, 

1762:		uint256[] memory _vaultRewardRates, 

1763:		uint256[] memory _identityCreditYields

1784:		string[] memory _identityCreditYields,

1785:		uint256[] memory _points

1803:		string[] memory _vaultCreditMultipliers,

1804:		uint256[] memory _multipliers

1820:		PoolConfigurationInput[] memory _inputs
```
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
## [G-02] Use `indexed` to save gas
Using `indexed` for value type event parameters (address, uint, bool) saves at least 80 gas in emitting the event (https://gist.github.com/Tomosuke0930/9bf61e01a8c3e214d95a9b84dcb41d97).

Note that for other types however (string, bytes), it is more expensive.

Instances: 6
```solidity
File: contracts/staking/BYTES2.sol

63: 		uint256 amount
```
- [contracts/staking/BYTES2.sol#L63](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L63)
```solidity
File: contracts/staking/NeoTokyoStaker.sol

542:		uint256 timelockOption,

543:		uint256 amountOrTokenId

555:		uint256 reward,

556:		uint256 tax

570:		uint256 amountOrTokenId
```
- [contracts/staking/NeoTokyoStaker.sol#L542](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L542)
- [contracts/staking/NeoTokyoStaker.sol#L543](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L543)
- [contracts/staking/NeoTokyoStaker.sol#L555](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L555)
- [contracts/staking/NeoTokyoStaker.sol#L556](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L556)
- [contracts/staking/NeoTokyoStaker.sol#L570](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L570)

&nbsp;
## [G-03] Expressions for constant values such as a call to `keccak256` should use `immutable` rather than `constant`
When left as `constant`, the value is re-calculated each time it is used instead of being converted to a constant at compile time. This costs an extra ~100 gas for each access.

Using `immutable` only incurs the gas costs for the computation at deploy time.

See [here](https://github.com/ethereum/solidity/issues/9232) for a detailed description of the issue.

Instances: 7
```solidity
File: contracts/staking/BYTES2.sol

37:	bytes32 public constant BURN = keccak256("BURN");

40:	bytes32 public constant ADMIN = keccak256("ADMIN");
```
- [contracts/staking/BYTES2.sol#L37](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37)
- [contracts/staking/BYTES2.sol#L40](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L40)
```solidity
File: contracts/staking/NeoTokyoStaker.sol

206:	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

209:	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(

214:	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

217:	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

220:	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```
- [contracts/staking/NeoTokyoStaker.sol#L206](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206)
- [contracts/staking/NeoTokyoStaker.sol#L209](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L209)
- [contracts/staking/NeoTokyoStaker.sol#L214](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L214)
- [contracts/staking/NeoTokyoStaker.sol#L217](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L217)
- [contracts/staking/NeoTokyoStaker.sol#L220](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L220)

&nbsp;
## [G-04] Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier such as `hasValidPermit` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost (2400 per instance).

Instances: 11
```solidity
File: contracts/staking/BYTES2.sol

140:	function burn (

162:	function changeStakingContractAddress (

173:	function changeTreasuryContractAddress (
```
- [contracts/staking/BYTES2.sol#L140](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L140)
- [contracts/staking/BYTES2.sol#L162](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162)
- [contracts/staking/BYTES2.sol#L173](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173)
```solidity
File: contracts/staking/NeoTokyoStaker.sol

1708:	function configureLP (

1721:	function lockLP () external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {

1737:	function configureTimelockOptions (

1760:	function configureIdentityCreditYields (

1783:	function configureIdentityCreditPoints (

1802: 	function configureVaultCreditMultipliers (

1819: 	function configurePools (

1851:	function configureCaps (
```
- [contracts/staking/NeoTokyoStaker.sol#L1708](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1708)
- [contracts/staking/NeoTokyoStaker.sol#L1721](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1721)
- [contracts/staking/NeoTokyoStaker.sol#L1737](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737)
- [contracts/staking/NeoTokyoStaker.sol#L1760](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1760)
- [contracts/staking/NeoTokyoStaker.sol#L1783](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1783)
- [contracts/staking/NeoTokyoStaker.sol#L1802](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1802)
- [contracts/staking/NeoTokyoStaker.sol#L1819](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1819)
- [contracts/staking/NeoTokyoStaker.sol#L1851](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1851)

&nbsp;
## [G-05] `_stringEquals` can be optimized

When checking for equality of two strings, one can use `keccak256` as follows:

```solidity
function _stringEquals(string memory _a, string memory _b) public pure returns (bool) {
    return keccak256(bytes(_a)) == keccak256(bytes(_b));
}
```

This saves ~20,000 gas on deployment when the original lengthy `_stringEquals` function is replaced, and ~150 gas per call. `_stringEquals` appears in `_stakeS1Citizen` which is in scope.

Instances: 1
```solidity
824:	function _stringEquals (
            string memory _a,
            string memory _b
        ) private pure returns (bool) {

            // ...

865:    }
```
- [contracts/staking/NeoTokyoStaker.sol#L824-865](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L824-L865)
