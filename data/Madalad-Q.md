# Non-Critical Summary
| |Issue|Instances|
|:-:|:-|:-:|
|[N-01]|Use `indexed` for event parameters|6|
|[N-02]|Use fixed compiler version|2|
|[N-03]|Use appropriate variable naming convention|5|
|[N-04]|Missing `address(0)` checks in constructor/initialize|2|
|[N-05]|Empty blocks should emit something|2|

Total issues: 5

Total instances: 17

&nbsp;
# Non-Critical Issues
## [N-01] Use `indexed` for event parameters

Index event fields make the field more quickly accessible to off-chain tools that parse events. 

If the variable is value type (uint, address, bool), then using `indexed` saves gas. Otherwise, each index field costs extra gas during emission.

Instances: 6
- [contracts/staking/BYTES2.sol#L63](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L63)
- [contracts/staking/NeoTokyoStaker.sol#L542](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L542)
- [contracts/staking/NeoTokyoStaker.sol#L543](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L543)
- [contracts/staking/NeoTokyoStaker.sol#L555](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L555)
- [contracts/staking/NeoTokyoStaker.sol#L556](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L556)
- [contracts/staking/NeoTokyoStaker.sol#L570](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L570)

&nbsp;
## [N-02] Use fixed compiler version

A floating pragma risks a different compiler version being used in production vs testing, which poses security risks.

Instances: 2
- [contracts/staking/BYTES2.sol#L2](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2)
- [contracts/staking/NeoTokyoStaker.sol#L2](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2)

&nbsp;
## [N-03] Use appropriate variable naming convention

According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#local-and-state-variable-names), `constant`/`immutable` variables should be named '"'with all capital letters with underscores separating words', whereas other variables ought to be named using CamelCase.

Instances: 5
- [contracts/staking/BYTES2.sol#L49](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L49)
- [contracts/staking/BYTES2.sol#L52](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L52)
- [contracts/staking/NeoTokyoStaker.sol#L232](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L232)
- [contracts/staking/NeoTokyoStaker.sol#L249](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L249)
- [contracts/staking/NeoTokyoStaker.sol#L255](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L255)

&nbsp;
## [N-04] Missing `address(0)` checks in constructor/initialize

Failing to check for invalid parameters on deployment may result in an erroneous input and require an expensive redeployment.

Instances: 2
- [contracts/staking/BYTES2.sol#L75](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75)
- [contracts/staking/NeoTokyoStaker.sol#L588](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588)

&nbsp;
## [N-05] Empty blocks should emit something

If the code cannot be refactored such that they no longer exist, the block should do something such as emitting an event.

Instances: 2
- [contracts/staking/BYTES2.sol#L193](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L193)
- [contracts/staking/BYTES2.sol#L207](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L207)
