### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Unnecessary cache when variable is used only once (~3) |3 |

Total 1 issues

### [G-01] Unnecessary cache when variable is used only once

When using a variable only once, gas can be saved by not caching the variable. This removes an unnecessary MLOAD.

#### FINDINGS

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L631-L632
```solidity
L631: IGenericGetter citizen = IGenericGetter(S1_CITIZEN);
L632: uint256 rewardRate = citizen.getRewardRateOfTokenId(_citizenId);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L638-L641
```solidity
L638: IGenericGetter vault = IGenericGetter(VAULT);
L639: string memory vaultMultiplier = (_vaultId != 0)
L640:     ? vault.getCreditMultiplier(_vaultId)
L641:     : "";
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L664-L667
```solidity
L664: IGenericGetter vault = IGenericGetter(VAULT);
L665: string memory vaultMultiplier = (_vaultId != 0)
L666:     ? vault.getCreditMultiplier(_vaultId)
L667:     : "";
```

#### Recommended mitigation steps

Eliminate the local variable.

```solidity
uint256 rewardRate = IGenericGetter(S1_CITIZEN).getRewardRateOfTokenId(_citizenId);
```