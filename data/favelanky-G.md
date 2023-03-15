## [G-01] Use ternary operator

Using ternary operator save ~2000 gas

```diff
- uint256 cap = VAULT_CAP;
- if (!citizenStatus.hasVault) {
-     cap = NO_VAULT_CAP;
- }
+ uint256 cap = citizenStatus.hasVault ? VAULT_CAP : NO_VAULT_CAP;
```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1062-L1065