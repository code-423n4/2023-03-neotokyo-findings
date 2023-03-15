### [L-01] `VAULT_CAP` and `NO_VAULT_CAP` can be set to `0`

If a user possessing the appropriate authorization mistakenly, intentionally, or due to being hacked, sets `VAULT_CAP` or `NO_VAULT_CAP` to `0`, this will result in the `_stakeBytes()` function will revert when it will be called in `stake()` function

```solidity
File: contracts\staking\NeoTokyoStaker.sol

function configureCaps (uint256 _vaultedCap,uint256 _unvaultedCap) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external {
	VAULT_CAP = _vaultedCap;
	NO_VAULT_CAP = _unvaultedCap;
}
```

```solidity
File: contracts\staking\NeoTokyoStaker.sol

function stake (AssetType _assetType,uint256 _timelockId,uint256,uint256,uint256) external nonReentrant {
		...
		function (uint256) _b = _stakeBytes;
		...
		function (uint256) _stake;
		assembly {
			...
				case 2 {
					_stake := _b
				}
			...
		}
		_stake(timelockOption);
	}

function _stakeBytes (uint256) private {
		...
		uint256 cap = VAULT_CAP;
		if (!citizenStatus.hasVault) {
			cap = NO_VAULT_CAP;
		}
		if (citizenStatus.stakedBytes + amount > cap) {
			revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
		}
		...
		uint256 cap = NO_VAULT_CAP;
		if (citizenStatus.stakedBytes + amount > cap) {
			revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
		}

		...
}
```

### ****[L-02] Missing an event to emit when critical parameters are changed.****

This can make it difficult for external systems and users to track and verify changes to the contract state.

```solidity
File: contracts\staking\BYTES2.sol

function changeStakingContractAddress (address _staker) hasValidPermit(UNIVERSAL, ADMIN) external {
	STAKER = _staker;
}

function changeTreasuryContractAddress (address _treasury) hasValidPermit(UNIVERSAL, ADMIN) external {
	TREASURY = _treasury;
}
```

```solidity
File: contracts\staking\NeoTokyoStaker.sol

function configureCaps (uint256 _vaultedCap,uint256 _unvaultedCap) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external {
	VAULT_CAP = _vaultedCap;
	NO_VAULT_CAP = _unvaultedCap;
}

function configureLP (address _lp) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
	if (lpLocked) {
		revert LockedConfigurationOfLP();
	}
	LP = _lp;
}
```

### [L-03] Critical Address Changes Should Use Two-step Procedure

To minimize errors, it is recommended to perform critical procedures with a two-step process. Critical operations without a two-step procedure are more prone to errors, so it is recommended to include a two-step procedure for such functions.

```solidity
File: contracts\staking\BYTES2.sol

function changeStakingContractAddress (address _staker) hasValidPermit(UNIVERSAL, ADMIN) external {
	STAKER = _staker;
}
function changeTreasuryContractAddress (address _treasury) hasValidPermit(UNIVERSAL, ADMIN) external {
	TREASURY = _treasury;
}
```

```solidity
File: contracts\staking\NeoTokyoStaker.sol

function configureLP (address _lp) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
	...
	LP = _lp;
}
```

### [QA-01] Refactor `if else` in for more gas efficiency and improve readability

After first `if` check `citizenVaultId != 0 && vaultId != 0` we can omit additional checks in next `else if` blocks. And refactor code more gas efficiently and improve readability. 

```solidity
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

Refactored

```solidity
if (citizenVaultId != 0) {
  if (vaultId != 0) {
      revert CitizenAlreadyHasVault(citizenVaultId, vaultId);
  }
	// here will be `if (citizenVaultId != 0 && vaultId == 0)`
  citizenStatus.hasVault = true;
  vaultId = citizenVaultId;
} else if (vaultId != 0) {
	// here we have `if (citizenVaultId == 0 && vaultId != 0)`
  _assetTransferFrom(VAULT, msg.sender, address(this), vaultId);
  citizenStatus.hasVault = true;
  citizenStatus.stakedVaultId = vaultId;
}
```

### [QA-02] Create a function for repetitive code

It improves code readability and saves gas on deployment.

```solidity
File: contracts\staking\NeoTokyoStaker.sol

+ function _getVaultMultiplier(uint256 _vaultId) private view returns (string memory) {
+	  if (_vaultId != 0) {
+		  IGenericGetter vault = IGenericGetter(VAULT);
+		  return vault.getCreditMultiplier(_vaultId);
+	  }
+	  return "";
+ }

function getCreditYield (uint256 _citizenId,uint256 _vaultId) public view returns (string memory) {
	...
	// Retrieve the credit rate multiplier of any associated Vault.
-	IGenericGetter vault = IGenericGetter(VAULT);
-	string memory vaultMultiplier = (_vaultId != 0)
-		? vault.getCreditMultiplier(_vaultId)
-		: "";

+	string memory vaultMultiplier = _getVaultMultiplier(_vaultId);
	...
}

function getConfiguredVaultMultiplier (uint256 _vaultId) public view returns (uint256) {
	// Retrieve the credit rate multiplier of the Vault.
-	IGenericGetter vault = IGenericGetter(VAULT);
-	string memory vaultMultiplier = (_vaultId != 0)
-		? vault.getCreditMultiplier(_vaultId)
-		: "";

+	string memory vaultMultiplier = _getVaultMultiplier(_vaultId);
	...
}
```

### **[QA-03] Missing non-zero address checks in the constructor for immutable variables**

```solidity
File: contracts\staking\BYTES2.sol

constructor (address _bytes,address _s1Citizen,address _staker,address _treasury) {
		BYTES1 = _bytes;
		S1_CITIZEN = _s1Citizen;
		STAKER = _staker;
		TREASURY = _treasury;
	}
```

****

```solidity
File: contracts\staking\NeoTokyoStaker.sol

constructor (address _bytes,address _s1Citizen,address _s2Citizen,address _lpToken,address _identity,address _vault,uint256 _vaultCap,uint256 _noVaultCap) {
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

### [QA-04] Stick to the NatSpec format for function description.

`All` NatSpec comments are missing the `@notice` keyword before the description.

missing `@return` parameters in NatSpec comments:

```solidity
File: contracts\staking\NeoTokyoStaker.sol
	/**
		This function supports retrieving the reward and tax earned by a particular 
		`_recipient` on a specific pool of type `_assetType`.

		@param _assetType The type of the asset to calculate rewards for.
		@param _recipient The recipient of the reward.
	*/
	function getPoolReward (
		AssetType _assetType,
		address _recipient
	) public view returns (uint256, uint256) {
```

### [QA-05] Floating pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### **[QA-06] Missing non-zero address checks in a critical function**

`configureLP()` does not include checks to ensure that the address provided for changing the LP token is non-zero.

```solidity
File: contracts\staking\NeoTokyoStaker.sol

function configureLP (address _lp) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
	if (lpLocked) {
		revert LockedConfigurationOfLP();
	}
	LP = _lp;
}
```

```solidity
File: contracts\staking\BYTES2.sol

function changeStakingContractAddress (address _staker) hasValidPermit(UNIVERSAL, ADMIN) external {
	STAKER = _staker;
}

function changeTreasuryContractAddress (address _treasury) hasValidPermit(UNIVERSAL, ADMIN) external {
	TREASURY = _treasury;
}
```

### **[QA-07] Address Zero-Check Missing in All External Functions**

Input parameter validation is a best practice. The most common validation is the address zero-check This validation helps prevent potential issues related to interacting with null addresses.

### [QA-08] Constant values such as a call to `keccak256()`, should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

```solidity
File: contracts\staking\BYTES2.sol

/// The identifier for the right to perform token burns.
bytes32 public constant BURN = keccak256("BURN");
/// The identifier for the right to perform some contract changes.
bytes32 public constant ADMIN = keccak256("ADMIN");
```

```solidity
File: contracts\staking\NeoTokyoStaker.sol

/// The identifier for the right to configure the LP token address.
bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
/// The identifier for the right to configure timelock options.
bytes32 public constant CONFIGURE_TIMELOCKS = keccak256("CONFIGURE_TIMELOCKS");
/// The identifier for the right to configure Identity and Vault points.
bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
/// The identifier for the right to configure emission rates and the DAO tax.
bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
/// The identifier for the right to configure BYTES staking caps.
bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```

### [QA-08] Use delete to clear struct/variables instead of zero assignment

You can use the delete keyword instead of setting the variable as zero.

```solidity
File: contracts\staking\NeoTokyoStaker.sol

function _withdrawS1Citizen () private {
		...
		// Validate that the caller has cleared their asset timelock.
		StakedS1Citizen storage stakedCitizen = stakedS1[msg.sender][citizenId];
		...
-		stakedCitizen.stakedBytes = 0;
-		stakedCitizen.timelockEndTime = 0;
-		stakedCitizen.points = 0;
-		stakedCitizen.hasVault = false;
-		stakedCitizen.stakedVaultId = 0;
+		delete stakedS1[msg.sender][citizenId];
		...
}

function _withdrawS2Citizen () private {
		...
		// Validate that the caller has cleared their asset timelock.
		StakedS2Citizen storage stakedCitizen = stakedS2[msg.sender][citizenId];
		...
-		stakedCitizen.stakedBytes = 0;
-		stakedCitizen.timelockEndTime = 0;
-		stakedCitizen.points = 0;
+		delete stakedS2[msg.sender][citizenId];
		...
}

function _withdrawLP () private {
		...
		// If all LP tokens are withdrawn, we must clear the multiplier.
		if (lpPosition.amount == 0) {
-			lpPosition.multiplier = 0;
+			delete lpPosition.multiplier;
		}
		...
}
```

### [QA-08] `Function writing` that does not comply with the `Solidity Style Guide`

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

[https://docs.soliditylang.org/en/v0.8.17/style-guide.html](https://docs.soliditylang.org/en/v0.8.17/style-guide.html)

Functions should be grouped according to their visibility and ordered:

constructor

receive function (if exists)

callback function (if exists)

external

public

internal

private

within a grouping, place the view and pure functions last

### **[**QA-09**]** Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. 

If `newCaps` dramatically increase or decrease users should be able to stake more if they want to. 

Consider adding a timelock to:

```solidity
File: contracts\staking\NeoTokyoStaker.sol

function configureCaps (uint256 _vaultedCap,uint256 _unvaultedCap) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external {
	VAULT_CAP = _vaultedCap;
	NO_VAULT_CAP = _unvaultedCap;
}
```

### [QA-10] For modern and more readable code; update import usage

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it.

This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces to allow us to apply this rule better.

**`import {contract1 , contract2} from "filename.sol";`**

```solidity
File: contracts\staking\BYTES2.sol

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

import "../access/PermitControl.sol";
import "../interfaces/IByteContract.sol";
import "../interfaces/IStaker.sol";
```

```solidity
File: contracts\staking\NeoTokyoStaker.sol

import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

import "../access/PermitControl.sol";
import "../interfaces/IByteContract.sol";
import "../interfaces/IGenericGetter.sol";
```

### **[**QA-11**] Use a single file for all system-wide constants**

There are many constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values). This will help with readability and easier maintenance for future changes.

### **[**QA-12**] Use a separate file for helper functions**

This will reduce SLOC and improve the readability of the contract. I think it is possible to split the contract into even more files to improve its readability.

Consider moving to a separate file:

```solidity
File: contracts\staking\NeoTokyoStaker.sol

function _assetTransferFrom (address _asset,address _from,address _to,uint256 _idOrAmount) private {
function _assetTransfer (address _asset,address _to,uint256 _amount) private {
function _stringEquals (string memory _a,string memory _b) private pure returns (bool) {
```

### [QA-13] Event is missing `indexed` fields

Indexed event fields make the field more quickly available to off-chain tools parsing events. Note, however, that each indexed field requires additional gas to emit, so it is not always best to index the maximum allowed for a single event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and the use of gas is of little concern for these events. If there are fewer than three fields, all fields must be indexed.

```solidity
File: contracts\staking\BYTES2.sol

event BytesUpgraded (
	address indexed caller,
	uint256 amount
);
```

```solidity
File: contracts\staking\NeoTokyoStaker.sol

event Stake (
	address indexed staker,
	address indexed asset,
	uint256 timelockOption,
	uint256 amountOrTokenId
);
event Claim (
	address indexed recipient,
	uint256 reward,
	uint256 tax
);
event Withdraw (
	address indexed caller,
	address indexed asset,
	uint256 amountOrTokenId
);
```

### [QA-14] Insufficient coverage

Testing all functions is best practice in terms of security criteria. 

BYTES2.sol | 78.95 % Stmts | 83.33 % Branch | 75 % Funcs | 71.43 % Lines