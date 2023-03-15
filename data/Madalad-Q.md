# Low Risk Summary
| |Issue|Instances|
|:-:|:-|:-:|
|[L-01]|Add timelock for critical parameter changes|8|
|[L-02]|CEI pattern not followed|3|
|[L-03]|Missing functionality to claim airdrops|1|

Total issues: 3

Total instances: 12

&nbsp;
# Non-Critical Summary
| |Issue|Instances|
|:-:|:-|:-:|
|[N-01]|Use `indexed` for event parameters|6|
|[N-02]|Use fixed compiler version|2|
|[N-03]|Use appropriate variable naming convention|5|
|[N-04]|Missing `address(0)` checks in constructor/initialize|2|
|[N-05]|Empty blocks should emit something|2|
|[N-06]|Missing events for critical parameter changes|10|

Total issues: 6

Total instances: 27

&nbsp;
# Low Risk Issues
## [L-01] Add timelock for critical parameter changes

Admin functions in `NeoTokyoStaker` have no timelock, meaning they can be called and critical parameters may be changed without notice. Users with ongoing stakes are affected by such changes and would be left unaware, and have no time to react.

In the worst case, a hacked/malicious admin can steal unclaimed rewards from arbitrary users and deposit them into the DAO treasury:
- call `configurePools` to set `daoTax` to `10000` (100%)
- call `getReward` with `_to` set to a whale with significant unclaimed rewards
    - 100% of the rewards will be sent to the DAO treasury
- call `configurePools` again with the original pool configuration to set the pools back to normal

Instances: 8
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
## [L-02] CEI pattern not followed

Each of the `private` withdraw functions (`_withdrawS1Citizen`, `_withdrawS2Citizen`, `_withdrawLP`) violate the [Checks Effects Interactions (CEI) pattern](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html). This is a best practice that protects against reentrancy (also [cross function reentrancy](https://medium.com/coinmonks/protect-your-solidity-smart-contracts-from-reentrancy-attacks-9972c3af7c21)). Should a future upgrade to the `BYTES` token include either a `_beforeTokenTransfer` or `_afterTokenTransfer` hook, these functions would become particularly vulnerable.

It is recommended to move external calls (`_assetTransfer` and `_assetTransferFrom`) to the end of the function, after the relevant state updates have been made so that the attack surface is minimised.

Instances: 3
```solidity
File: contracts/staking/NeoTokyoStaker.sol

1459:	function _withdrawS1Citizen () private {

1534:	function _withdrawS2Citizen () private {

1597:	function _withdrawLP () private {
```
- [contracts/staking/NeoTokyoStaker.sol#L1459](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1476-L1492)
- [contracts/staking/NeoTokyoStaker.sol#L1534](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1551-L1557)
- [contracts/staking/NeoTokyoStaker.sol#L1597](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1618)

&nbsp;
## [L-03] Missing functionality to claim airdrops

Since the `NeoTokyoStaker` contract holds assets during their stake, any potential future airdrops for citizens or BYTES will go to that address. Currently, there is no functionality for users to claim the airdrops for their staked assets, nor for permitted users to sweep tokens from the contract, meaning any airdropped assets would be irretrievable.

Instances: 1
```solidity
File: contracts/staking/NeoTokyoStaker.sol
```
- [contracts/staking/NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)

&nbsp;

&nbsp;

# Non-Critical Issues
## [N-01] Use `indexed` for event parameters

Index event fields make the field more quickly accessible to off-chain tools that parse events. 

If the variable is value type (uint, address, bool), then using `indexed` saves gas. Otherwise, each index field costs extra gas during emission.

Instances: 6
```solidity
File: contracts/staking/BYTES2.sol

63:		uint256 amount
```
- [contracts/staking/BYTES2.sol#L63](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L63)
```solidity
File: contracts/staking/NeoTokyoStaking.sol

542: 		uint256 timelockOption,

543: 		uint256 amountOrTokenId

555:		uint256 reward,

556: 		uint256 tax

570:		uint256 amountOrTokenId
```
- [contracts/staking/NeoTokyoStaker.sol#L542](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L542)
- [contracts/staking/NeoTokyoStaker.sol#L543](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L543)
- [contracts/staking/NeoTokyoStaker.sol#L555](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L555)
- [contracts/staking/NeoTokyoStaker.sol#L556](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L556)
- [contracts/staking/NeoTokyoStaker.sol#L570](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L570)

&nbsp;
## [N-02] Use fixed compiler version

A floating pragma risks a different compiler version being used in production vs testing, which poses security risks.

Instances: 2
```solidity
File: contracts/staking/BYTES2.sol

2: pragma solidity ^0.8.19;
```
- [contracts/staking/BYTES2.sol#L2](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2)
```solidity
File: contracts/staking/NeoTokyoStaker.sol

2: pragma solidity ^0.8.19;
```
- [contracts/staking/NeoTokyoStaker.sol#L2](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2)

&nbsp;
## [N-03] Use appropriate variable naming convention

According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#local-and-state-variable-names), `constant`/`immutable` variables should be named '"'with all capital letters with underscores separating words', whereas other variables should to be named using CamelCase.

Instances: 5
```solidity
File: contracts/staking/BYTES2.sol

49: 	address public STAKER;

52: 	address public TREASURY;
```
- [contracts/staking/BYTES2.sol#L49](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L49)
- [contracts/staking/BYTES2.sol#L52](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L52)
```solidity
File: contracts/staking/NeoTokyoStaker.sol

232: 	address public LP;

249: 	uint256 public VAULT_CAP;

255: 	uint256 public NO_VAULT_CAP;
```
- [contracts/staking/NeoTokyoStaker.sol#L232](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L232)
- [contracts/staking/NeoTokyoStaker.sol#L249](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L249)
- [contracts/staking/NeoTokyoStaker.sol#L255](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L255)

&nbsp;
## [N-04] Missing `address(0)` checks in constructor/initialize

Failing to check for invalid parameters on deployment may result in an erroneous input and require an expensive redeployment.

Instances: 2
```solidity
File: contracts/staking/BYTES2.sol

75:     constructor (
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
- [contracts/staking/BYTES2.sol#L75](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75)
```solidity
File: contracts/staking/NeoTokyoStaker.sol

588:	constructor (
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
```
- [contracts/staking/NeoTokyoStaker.sol#L588](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588)

&nbsp;
## [N-05] Empty blocks should emit something

If the code cannot be refactored such that they no longer exist, the block should do something such as emitting an event.

Instances: 2
```solidity
File: contracts/staking/BYTES2.sol

193: 	function updateReward (
            address,
            address,
            uint256
        ) external {
        }

207:	function updateRewardOnMint (
            address,
            uint256
        ) external {
        }
```
- [contracts/staking/BYTES2.sol#L193](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L193)
- [contracts/staking/BYTES2.sol#L207](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L207)

&nbsp;
## [N-06] Missing events for critical parameter changes

Events help non-contract tools to track changes, and prevent users from being surprised by changes.

Instances: 10
```solidity
File: contracts/staking/BYTES2.sol

162:	function changeStakingContractAddress (

173:	function changeTreasuryContractAddress (
```
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
