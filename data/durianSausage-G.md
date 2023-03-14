# durianSausage Neo Tokyo contest

# Gas
## G01： Use assembly to write address storage values

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L598-L605
```
		BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
		LP = _lpToken;
		IDENTITY = _identity;
		VAULT = _vault;
		VAULT_CAP = _vaultCap;
		NO_VAULT_CAP = _noVaultCap;
```

## G02: >= costs less gas than >
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1215
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas
```
if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
```

## G03： Change public state variable visibility to private
If it is preferred to change the visibility of the owner and pendingOwnerstate state variables to private, this will save significant gas.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L209


## G04： sing UniswapV3 mulDiv function is gas-optimized
Reference: https://github.com/Uniswap/v3-core/blob/412d9b236a1e75a98568d49b1aeb21e3a1430544/contracts/libraries/FullMath.sol#L14

Reference: https://xn—2-umb.com/21/muldiv/


## G05：Gas overflow during iteration (DoS)
Each iteration of the cycle requires a gas flow. A moment may come when more gas is required than it is allocated to record one block. In this case, all iterations of the loop will fail.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1499

## G06： Use nested if and, avoid multiple check combinations
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L910

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.


