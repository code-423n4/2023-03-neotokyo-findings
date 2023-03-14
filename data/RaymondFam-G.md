## calldata and memory
When running a function we could pass the function parameters as calldata or memory for variables such as strings, bytes, structs, arrays etc. If we are not modifying the passed parameter, we should pass it as calldata because calldata is more gas efficient than memory.

Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. However, it alleviates the compiler from the `abi.decode()` step that copies each index of the calldata to the memory index, bypassing the cost of 60 gas on each iteration. 

For example, the instances below may be refactored as follows:

[File: NeoTokyoStaker.sol#L1802-L1810](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1802-L1810)

```diff
	function configureVaultCreditMultipliers (
-		string[] memory _vaultCreditMultipliers,
+		string[] calldata _vaultCreditMultipliers,
-		uint256[] memory _multipliers
+		uint256[] calldata _multipliers
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
		for (uint256 i; i < _vaultCreditMultipliers.length; ) {
			vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];
			unchecked { ++i; }
		}
	}
```
## += and -= cost more gas
`+=` and `-=` generally cost 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

For example, the `+=` instance below may be refactored as follows:

[File: NeoTokyoStaker.sol#L977](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L977)

```diff
-	pool.totalPoints += citizenStatus.points;
+	pool.totalPoints = pool.totalPoints + citizenStatus.points;
```
Similarly, the `-=` instance below may be refactored as follows:

[File: NeoTokyoStaker.sol#L1626](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1626)

```diff
-	lpPosition.amount -= amount;
+	lpPosition.amount = lpPosition.amount - amount;
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, the following `>=` inequality instance may be refactored as follows:

[File: NeoTokyoStaker.sol#L1215](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1215)

```diff
-	if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
// Rationale for subtracting 1 on the right side of the inequality:
// x >= 10; [10, 11, 12, ...]
// x > 10 - 1 is the same as x > 9; [10, 11, 12 ...]
+	if (_pools[_assetType].rewardWindows[0].startTime > block.timestamp - 1) {
```
Similarly, the `<=` instance below may be replaced with `<` as follows:

[File: NeoTokyoStaker.sol#L1329](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1329)

```diff
-	if (block.timestamp <= window.startTime) {
// Rationale for adding 1 on the right side of the inequality:
// x <= 10; [10, 9, 8, ...]
// x < 10 + 1 is the same as x < 11; [10, 9, 8 ...]
+	if (block.timestamp < window.startTime + 1) {
```
## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the instance below may be refactored as follows:

[File: NeoTokyoStaker.sol#L926](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L926)

```diff
-	} else if (citizenVaultId == 0 && vaultId != 0) {
+	} else if (!(citizenVaultId != 0 || vaultId == 0)) {
```