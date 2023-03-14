## Minimization of truncation
Multiple divisions in an arithmetic calculations may be reduced to one division to minimize the frequency of truncation. 

For example, the following two instances may be refactored as follows:

[File: NeoTokyoStaker.sol#L1155](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155)

```diff
-	uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
+	uint256 points = amount * 100 * timelockMultiplier / (_DIVISOR * 1e18);
```
[File: NeoTokyoStaker.sol#L1623](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1623)

```diff
-	uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
+	uint256 points = amount * 100 * lpPosition.multiplier / (_DIVISOR * 1e18);
```
## Typo mistakes
[File: NeoTokyoStaker.sol#L619](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L619)

```diff
-	if there is one. This parameter is separated to optimized for callers who
+	if there is one. This parameter is separated to optimize for callers who
```
[File: NeoTokyoStaker.sol#L1373](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1373)

```diff
-					Otherwise, the last reward rate, and therefore the entireity of 
+					Otherwise, the last reward rate, and therefore the entirety of 
```
