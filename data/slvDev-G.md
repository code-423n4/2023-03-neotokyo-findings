|  | Issue | Instances |
| --- | --- | --- |
| [G-01] | Avoid code duplication - event emitting | 7 |
| [G-02] | Use constants instead of type(uintx).max | 3 |
| [G-03] | Use nested if, avoid multiple check combinations | 2 |
| [G-04] | Setting the constructor to payable | 2 |
| [G-05] | Use assembly to write address storage values | 6 |
| [G-06] | Using storage instead of memory for structs/arrays saves gas | 3 |
| [G-07] | Functions guaranteed to revert_ when called by normal users can be marked payable | 11 |
| [G-08] | x += y (x -= y) costs more gas than x = x + y (x = x - y) for state variables | 9 |
| [G-09] | Upgrade Solidity’s optimizer | 1 |

### [G-01] Avoid code duplication - event emitting

Creating a separate internal function to emit an event, the potential gas efficiency comes from reducing code duplication, which can lead to a smaller bytecode size. This can be more gas-efficient because the EVM will need to store and execute less duplicated code.

NeoTokyoStaker contract Deployment gas cost: 4019944 - > 3978315

And slightly reduce `stake()` gas cost.

```solidity
File: contracts\staking\NeoTokyoStaker.sol

+    function _emitStakeEvent(address staker, address asset, uint256 timelock, uint256 id) internal {
+      emit Stake(staker, asset, timelock, id);
+    }

-  emit Stake(msg.sender,S1_CITIZEN,_timelock,citizenId);
-  emit Stake(msg.sender,S2_CITIZEN,_timelock,citizenId);
-  emit Stake(msg.sender,BYTES,(seasonId << 128) + citizenId,amount);
-  emit Stake(msg.sender,LP,_timelock,amount);
```

Deployment gas cost: 4019944 - > 4004555

```solidity
+		function _emitWithdrawEvent(address staker, address asset, uint256 id) internal {
+			emit Withdraw(staker, asset, id);
+		}

-  emit Withdraw(msg.sender,S1_CITIZEN,	citizenId);
-  emit Withdraw(msg.sender,S2_CITIZEN,citizenId);
-  emit Withdraw(msg.sender,LP,amount);
```

Overall: 4019944  - > 3964018

### [G-02] Use constants instead of type(uintx).max

Using type(uint16).max and similar operations consume more gas during both the distribution process and for each transaction compared to using constants.

```solidity
File: contracts\staking\NeoTokyoStaker.sol

963:		uint256 timelockMultiplier = _timelock & type(uint128).max;

1017:   uint256 timelockMultiplier = _timelock & type(uint128).max;

1141:   uint256 timelockMultiplier = _timelock & type(uint128).max;
```

### [G-03] Use nested `if`, avoid multiple check combinations

Using nested is cheaper than using `&&` multiple check combinations if it ****is not followed by an `else` statement. There are more advantages, such as easier-to-read code and better coverage reports.

```solidity
File: contracts\staking\NeoTokyoStaker.sol

926:        } else if (citizenVaultId == 0 && vaultId != 0) {

1836:       if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
```

### [G-04] ****Setting the *constructor* to `payable`**

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of `msg.value == 0` and saves gas on deployment with no security risks.

```solidity
File: contracts\staking\NeoTokyoStaker.sol

constructor (address _bytes,address _s1Citizen,address _s2Citizen,address _lpToken,address _identity,address _vault,uint256 _vaultCap,uint256 _noVaultCap) {

```

```solidity
File: contracts\staking\BYTES2.sol

constructor (address _bytes,address _s1Citizen,address _staker,address _treasury) {

```

### [G-05] Use `assembly` to write *address storage values*

If the `address` variable is mutable.

```solidity
File: contracts\staking\BYTES2.sol

constructor (address _bytes,address _s1Citizen,address _staker,address _treasury) {
		BYTES1 = _bytes;
		S1_CITIZEN = _s1Citizen;
-		STAKER = _staker;
-		TREASURY = _treasury;
+		assembly {
+        sstore(STAKER.slot, _staker)
+        sstore(TREASURY.slot, _treasury)
+    }
}

function changeStakingContractAddress (address _staker) hasValidPermit(UNIVERSAL, ADMIN) external {
-		STAKER = _staker;
+		assembly {
+			sstore(STAKER.slot, _staker)
+		}
}

function changeTreasuryContractAddress (address _treasury) hasValidPermit(UNIVERSAL, ADMIN) external {
-		TREASURY = _treasury;
+		assembly {
+			sstore(TREASURY.slot, _treasury)
+		}	
}
```

```solidity
File: contracts\staking\NeoTokyoStaker.sol

constructor (address _bytes,address _s1Citizen,address _s2Citizen,address _lpToken,address _identity,address _vault,uint256 _vaultCap,uint256 _noVaultCap) {
		BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
-		LP = _lpToken;
+		assembly {
+			sstore(LP.slot, _lpToken)
+		}	
		IDENTITY = _identity;
		VAULT = _vault;
		VAULT_CAP = _vaultCap;
		NO_VAULT_CAP = _noVaultCap;
}

function configureLP (address _lp) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
-		LP = _lp;
+		assembly {
+			sstore(LP.slot, _lp)
+		}	
}
```

### [G-06] Using `storage` instead of `memory` for `structs/arrays` saves gas

*note: Automated findings tool found something completely different and with the wrong instances count.*

```solidity
File: contracts\staking\NeoTokyoStaker.sol

1281:      StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];

1290:      StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];

1313:      RewardWindow memory window = pool.rewardWindows[i];
```

### [G-07] Functions guaranteed to revert_ when called by normal users can be marked `payable`

If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

```solidity
File: contracts\staking\BYTES2.sol

140:      function burn (address _from,uint256 _amount) hasValidPermit(UNIVERSAL, BURN) external {
162:      function changeStakingContractAddress (address _staker) hasValidPermit(UNIVERSAL, ADMIN) external {
173:      function changeTreasuryContractAddress (address _treasury) hasValidPermit(UNIVERSAL, ADMIN) external {
```

```solidity
File: contracts\staking\NeoTokyoStaker.sol

1708:      function configureLP (address _lp) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
1721:      function lockLP () external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
1737:      function configureTimelockOptions (AssetType _assetType,uint256[] memory _timelockIds,uint256[] memory _encodedSettings) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
1760:      function configureIdentityCreditYields (uint256[] memory _citizenRewardRates, string[] memory _vaultRewardRates,string[] memory _identityCreditYields) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1783:      function configureIdentityCreditPoints (string[] memory _identityCreditYields,uint256[] memory _points) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1802:      function configureVaultCreditMultipliers (string[] memory _vaultCreditMultipliers,uint256[] memory _multipliers) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1819:      function configurePools (PoolConfigurationInput[] memory _inputs) hasValidPermit(UNIVERSAL, CONFIGURE_POOLS) external {
1851:      function configureCaps (uint256 _vaultedCap,uint256 _unvaultedCap) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external {
```

### [G-08] `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables

```solidity
File: contracts\staking\NeoTokyoStaker.sol

977:      pool.totalPoints += citizenStatus.points;

1029:     pool.totalPoints += citizenStatus.points;

1078:     citizenStatus.stakedBytes += amount;
1079:  		citizenStatus.points += bonusPoints;
1080:			pool.totalPoints += bonusPoints;

1099:     citizenStatus.stakedBytes += amount;
1100:			citizenStatus.points += bonusPoints;
1101:			pool.totalPoints += bonusPoints;

1164:     pool.totalPoints += points;
```

### [G-09] Upgrade Solidity’s optimizer

Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.

Set the optimization value higher than 800 in your hardhat.config.ts file.

After increase up to 1000: `stake()` avg gas: 277026 → 276829

```jsx
File: hardhat.config.js

optimizer: {
  enabled: true,
- runs: 200,
+ runs: 1000,
  details: {
    yul: true,
  },
},
```