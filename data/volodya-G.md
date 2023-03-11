| Number | Optimization Details                                                                 |  Context  |
| :----: | :----------------------------------------------------------------------------------- | :-------: |
| [G-01] | simplify `if` condition                                                              |     1     |
| [G-02] | remove redundant variables                                                           |    11     |
| [G-03] | Reorder the `if` statements for `revert` so function will not do redundant execution |     2     |
| [G-04] | Use assembly to write address storage values                                         |     7     |
| [G‑05] | Multiple accesses of a mapping/array should use a local variable cache               |     1     |
| [G‑06] | Empty blocks should be removed or emit something                                     |     2     |
| [G‑07] | Function Ordering                                                                    | All files |

Total 24 issues
in addition to these known issues https://gist.github.com/Picodes/01427c59b07c651699136589541159a7
### [G-01] simplify `if` condition

1 results - 1 files:

```solidity
contracts/staking/NeoTokyoStaker.sol:910
		if (citizenVaultId != 0 && vaultId != 0) {
			revert CitizenAlreadyHasVault(citizenVaultId, vaultId);

		/*
			If no optional vault is provided, and the S1 Citizen being staked already
			has an existing Vault, override the provided `vaultId`.
		*/
		} else if (citizenVaultId != 0 && vaultId == 0) {
			citizenStatus.hasVault = true;
			vaultId = citizenVaultId;

		/*
			Otherwise, if the S1 Citizen has no component Vault, the newly-provided
			Vault is staked and the S1 Citizen is recorded as carrying an optional,
			separately-attached vault.
		*/
		} else if (citizenVaultId == 0 && vaultId != 0) {
			_assetTransferFrom(VAULT, msg.sender, address(this), vaultId);
			citizenStatus.hasVault = true;
			citizenStatus.stakedVaultId = vaultId;
		}
```

We can refactor to this

```solidity
contracts/staking/NeoTokyoStaker.sol:910
		if (citizenVaultId != 0 ) {
			/*
				If no optional vault is provided, and the S1 Citizen being staked already
				has an existing Vault, override the provided `vaultId`.
			*/
			if (vaultId != 0) {revert CitizenAlreadyHasVault(citizenVaultId, vaultId);}
			/*
                Otherwise, if the S1 Citizen has no component Vault, the newly-provided
                Vault is staked and the S1 Citizen is recorded as carrying an optional,
                separately-attached vault.
            */
			else if (vaultId == 0){
				citizenStatus.hasVault = true;
				vaultId = citizenVaultId;
			}

		} else if (vaultId != 0) {
			_assetTransferFrom(VAULT, msg.sender, address(this), vaultId);
			citizenStatus.hasVault = true;
			citizenStatus.stakedVaultId = vaultId;
		}
```

[contracts/staking/NeoTokyoStaker.sol:910](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L910)

### [G-02] remove redundant variables

11 results - 1 files:

We can omit `identityPoints` variable, it used once and readability will not become worse

```diff
contracts/staking/NeoTokyoStaker.sol:943
-943		uint256 identityPoints = identityCreditPoints[citizenCreditYield];
-969				identityPoints * vaultMultiplier * timelockMultiplier /
+969				identityCreditPoints[citizenCreditYield] * vaultMultiplier * timelockMultiplier /
```

[contracts/staking/NeoTokyoStaker.sol:943](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L943)

We can omit `pool` variable, it used once and readability will become better.

```diff
contracts/staking/NeoTokyoStaker.sol:966
-966		PoolData storage pool = _pools[AssetType.S1_CITIZEN];
-977		pool.totalPoints += citizenStatus.points;
+977		_pools[AssetType.S1_CITIZEN].totalPoints += citizenStatus.points;
```

[contracts/staking/NeoTokyoStaker.sol:966](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L966)
We can safely omit these as well

```diff
contracts/staking/NeoTokyoStaker.sol:966
1021		PoolData storage pool = _pools[AssetType.S2_CITIZEN];
1076		PoolData storage pool = _pools[AssetType.S1_CITIZEN];
1097		PoolData storage pool = _pools[AssetType.S2_CITIZEN];
1154		PoolData storage pool = _pools[AssetType.LP];
1514		PoolData storage pool = _pools[AssetType.S1_CITIZEN];
1579		PoolData storage pool = _pools[AssetType.S2_CITIZEN];
1622		PoolData storage pool = _pools[AssetType.LP];

```

[contracts/staking/NeoTokyoStaker.sol:966](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L966)

We can omit `s1Citizen` variable, it used once and readability will not become worse

```diff
contracts/staking/NeoTokyoStaker.sol:1281
-1280					StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
1281					unchecked {
-1282						points += s1Citizen.points;
+1282						points += stakedS1[_recipient][citizenId].points;
1283						i++;
					}
```

[contracts/staking/NeoTokyoStaker.sol:1281](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1281)

The same here

```diff
contracts/staking/NeoTokyoStaker.sol:1289
				uint256 citizenId = _stakerS2Position[_recipient][i];
```

[contracts/staking/NeoTokyoStaker.sol:1289](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1289)

### [G-03] Reorder the `if` statements for `revert` so function will not do redundant execution

2 results - 1 files:

```solidity
contracts/staking/NeoTokyoStaker.sol:1060
		if (seasonId == 1) {
			StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
			uint256 cap = VAULT_CAP;
			if (!citizenStatus.hasVault) {
				cap = NO_VAULT_CAP;
			}
			if (citizenStatus.stakedBytes + amount > cap) {
				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
			}

			// Validate that the caller actually staked the Citizen.
			if (citizenStatus.timelockEndTime == 0) {
				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
			}

			PoolData storage pool = _pools[AssetType.S1_CITIZEN];
			unchecked {
				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
				citizenStatus.stakedBytes += amount;
				citizenStatus.points += bonusPoints;
				pool.totalPoints += bonusPoints;
			}

		// Handle staking BYTES into an S2 Citizen.
		}
```

Suggestion

```solidity
contracts/staking/NeoTokyoStaker.sol:1060
		if (seasonId == 1) {
			StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];

			// Validate that the caller actually staked the Citizen.
			if (citizenStatus.timelockEndTime == 0) {
				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
			}

			uint256 cap = VAULT_CAP;
			if (!citizenStatus.hasVault) {
				cap = NO_VAULT_CAP;
			}
			if (citizenStatus.stakedBytes + amount > cap) {
				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
			}



			PoolData storage pool = _pools[AssetType.S1_CITIZEN];
			unchecked {
				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
				citizenStatus.stakedBytes += amount;
				citizenStatus.points += bonusPoints;
				pool.totalPoints += bonusPoints;
			}

		// Handle staking BYTES into an S2 Citizen.
		}
```

[contracts/staking/NeoTokyoStaker.sol:1060](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1060)
Same here, move to top if it`s scope

```solidity
contracts/staking/NeoTokyoStaker.sol:1060
			if (citizenStatus.timelockEndTime == 0) {
```

[contracts/staking/NeoTokyoStaker.sol:1092](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1092)

### [G-04] Use assembly to write address storage values

5 results - 2 files:

```diff
contracts/staking/NeoTokyoStaker.sol:1708
	function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
-1714		LP = _lp;
+1715		assembly {
+1716			sstore(LP.slot, _lp)
+1717		}
	}
```

```sodlitiy
contracts/staking/BYTES2.sol:165
		STAKER = _staker;

```

[contracts/staking/BYTES2.sol:165](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L165)
Same for these

```solidity
contracts/staking/BYTES2.sol
81		BYTES1 = _bytes;
82		S1_CITIZEN = _s1Citizen;
83		STAKER = _staker;
84		TREASURY = _treasury;

165     STAKER = _staker;
```

[contracts/staking/BYTES2.sol:75](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L75)

### [G-05] Multiple accesses of a mapping/array should use a local variable cache

Cache this variable just like its dont in whole file
1 results - 1 files:

```diff
contracts/staking/NeoTokyoStaker.sol:1824
-1824			_pools[_inputs[i].assetType].rewardCount = poolRewardWindowCount;
+1824			PoolData storage pool = _pools[_inputs[i].assetType];
+1825			pool.rewardCount = poolRewardWindowCount;
+1826			pool.daoTax = _inputs[i].daoTax;
...
	}
```

[contracts/staking/NeoTokyoStaker.sol:1824](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1824)

### [G-06] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

2 results - 1 files:

```solidity
contracts/staking/BYTES2.sol:189
189     function updateReward (
204     function updateRewardOnMint (

	}
```

[contracts/staking/BYTES2.sol:189](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L189)

### [G-07] Function Ordering

When a function is called, the EVM compares the method ID of the transaction to the method ID's present in the contract, with each comparison costing an additional 22 gas. These comparisons are performed in order from least to greatest byte value of method ID's (alphanumerically based on the method ID). This is done using binary search.

[Link](https://github.com/KadenZipfel/gas-optimizations/blob/main/gas-golfing/function-ordering.md)
