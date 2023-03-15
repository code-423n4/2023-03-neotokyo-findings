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
## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: NeoTokyoStaker.sol#L659-L671](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L659-L671)

```diff
	function getConfiguredVaultMultiplier (
		uint256 _vaultId
-	) public view returns (uint256) {
+	) public view returns (uint256 _vaultCreditMultiplier) {

		// Retrieve the credit rate multiplier of the Vault.
		IGenericGetter vault = IGenericGetter(VAULT);
		string memory vaultMultiplier = (_vaultId != 0)
			? vault.getCreditMultiplier(_vaultId)
			: "";
		
		// Deduce the configured Vault multiplier.
-		return vaultCreditMultiplier[vaultMultiplier];
+		_vaultCreditMultiplier = vaultCreditMultiplier[vaultMultiplier];
	}
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
version: "0.8.19",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.