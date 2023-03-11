## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]|Low-level calls that are unnecessary for the system should be avoided | 2 |
|[L-02]|Insufficient coverage |  |
|[L-03]|Project Upgrade and Stop Scenario should be |  |
|[L-04]|Division before multiplication causing significant loss of precision |1 |
|[L-05]|Add to Event-Emit for critical functions| 2 |
|[L-06]|Update codes to avoid Compile Errors| 3 |
|[L-07]|`Claim` event is missing parameters| 1 |
|[L-08]|Project has NPM Dependency which uses a vulnerable version : `@openzeppelin`| 1 |
|[L-09]|Keccak Constant values should used to immutable rather than constant | 7 |
|[L-10]|In the `constructor` , there is no return of incorrect address identification | 1 |

Total 10 issues

### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01] |Omissions in Events|1|
| [N-02] |`Function writing` that does not comply with the `Solidity Style Guide` ||
| [N-03] |Tokens accidentally sent to the contract cannot be recovered| All Contracts |
| [N-04] |Floating pragma| 2 |
| [N-05] |Use SMTChecker| |
| [N-06] |Constants on the left are better| 4 |
| [N-07] |`constants` should be defined rather than using magic numbers| 3 |
| [N-08] |Use the `delete` keyword instead of assigning a value of `0`|8 |
| [N-09] |Use a single file for all system-wide constants |12 |
| [N-10] |Function Calls in Loop Could Lead to Denial of Service due to Array length not being checked| 1  |
| [N-11] |According to the syntax rules, use ` => mapping (` instead of ` => mapping(` using spaces as keyword|2 |
| [N-12] |For modern and more readable code; update import usages| 9 |
| [N-13] |Assembly Codes Specific – Should Have Comments | 7 |
| [N-14] |Take advantage of Custom Error's return value property| 3 |
| [N-15] |Pragma version^0.8.19  version too recent to be trusted| 1 |
| [N-16] |Inconsistent Solidity Versions | 21 |

Total 16 issues


### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |You can explain the operation of critical functions in NatSpec with an infographic |

Total 1 suggestion


### [L-01] Low-level calls that are unnecessary for the system should be avoided 

Low-level calls that are unnecessary for the system should be avoided whenever possible because low-level calls behave differently from a contract-type call. For example;

address.call(abi.encodeWithSelector("fancy(bytes32)", mybytes) does not verify that a target is actually a contract, while
ContractInterface(address).fancy(mybytes) does. 

Additionally, when calling out to functions declared view/pure, the solidity compiler would actually perform a staticcall providing additional security guarantees while a low-level call does not. Similarly, return values have to be decoded manually when performing low-level calls.

Note: if a low-level call needs to be performed, consider relying on Contract.function.selector instead of encoding using a hardcoded ABI string.

```solidity

2 results

contracts/staking/NeoTokyoStaker.sol:
  772: 		(bool success, bytes memory data) = 
  773: 			_asset.call(
  774: 				abi.encodeWithSelector(
  775: 					_TRANSFER_FROM_SELECTOR,
  776: 					_from,
  777: 					_to, 
  778: 					_idOrAmount
  779: 				)
  780: 			);
  781: 
  782: 		// Revert if the low-level call fails.
  783: 		if (!success) {
  784: 			revert(string(data));
  785: 		}




 801: 		(bool success, bytes memory data) = 
  802: 			_asset.call(
  803: 				abi.encodeWithSelector(
  804: 					_TRANSFER_SELECTOR,
  805: 					_to, 
  806: 					_amount
  807: 				)
  808: 			);
  809: 
  810: 		// Revert if the low-level call fails.
  811: 		if (!success) {
  812: 			revert(string(data));
  813: 		}
  814: 	}


```

Recommendation
When calling out to a contract known in the system, always prefer
typed contract calls (interfaces/contract) instead of low-level calls.

This is to avoid errors, potentially unchecked return values, have security guarantees 


### [L-02] Insufficient coverage

**Description:**
The test coverage rate of the project is ~85%. Testing all functions is best practice in terms of security criteria.
Due to its capacity, test coverage is expected to be 100%.


```js
---------------------------|----------|----------|----------|----------|----------------|
File                       |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
---------------------------|----------|----------|----------|----------|----------------|
 staking/                  |       95 |    74.53 |    88.57 |    89.84 |                |
  BYTES2.sol               |    78.95 |    83.33 |       75 |    71.43 |... 151,152,154 |
  NeoTokyoStaker.sol       |    96.09 |       74 |    92.59 |    90.96 |... 5,1855,1856 |
---------------------------|----------|----------|----------|----------|----------------|

```

### [L-03] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

### [L-04] Division before multiplication causing significant loss of precision


 first divides and then multiplies again, there is a significant loss of precision;

```solidity

contracts/staking/NeoTokyoStaker.sol:
  1385  
  1386: 			// Return final shares.
  1387: 			unchecked {
  1388: 				uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
  1389: 				uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
  1390: 				share /= _PRECISION;
  1391: 				daoShare /= _PRECISION;
  1392: 				return ((share - daoShare), daoShare);
  1393: 			}
  1394  		}
```



## Tools Used
Manual code review

## Recommended Mitigation Steps
Multiply first before dividing to keep the precision.

### [L-05] Add to Event-Emit for critical functions


```solidity
2 results

contracts/staking/BYTES2.sol:
  161  	*/
  162: 	function changeStakingContractAddress (
  163: 		address _staker
  164: 	) hasValidPermit(UNIVERSAL, ADMIN) external {
  165: 		STAKER = _staker;
  166: 	}
  167: 
  168: 	/**
  169: 		Allow a permitted caller to update the treasury address.
  170: 
  171: 		@param _treasury The address of the new treasury.
  172: 	*/
  173: 	function changeTreasuryContractAddress (
  174: 		address _treasury
  175: 	) hasValidPermit(UNIVERSAL, ADMIN) external {
  176: 		TREASURY = _treasury;
  177: 	}


```


### [L-06] Update codes to avoid Compile Errors


```js
Warning: Function state mutability can be restricted to pure
   --> contracts/staking/BYTES2.sol:245:2:
    |
245 |   function updateReward (
    |   ^ (Relevant source part starts here and spans across multiple lines).


Warning: Function state mutability can be restricted to pure
   --> contracts/staking/BYTES2.sol:261:2:
    |
261 |   function updateRewardOnMint (
    |   ^ (Relevant source part starts here and spans across multiple lines).

Warning: Contract code size is 63151 bytes and exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on Mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
   --> contracts/staking/NeoTokyoStaker.sol:190:1:
    |
190 | contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
    | ^ (Relevant source part starts here and spans across multiple lines).

```
### [L-07] `Claim` event is missing parameters


The `NeoTokyoStaker.sol` contract has very important function; `Claim` 


However, only amounts are published in emits, whereas msg.sender must be specified in every transaction, a contract or web page listening to events cannot react to users, emit does not serve the purpose.
Basically, this event cannot be used


```diff
contracts/staking/NeoTokyoStaker.sol:
  1444  
  1445: 		// Emit an event.
  1446: 		emit Claim (
+  			msg.sender
  1447: 			_recipient,
  1448: 			totalReward,
  1449: 			totalTax
  1450: 		);


```



### Recommended Mitigation Steps
add `msg.sender` parameter in event-emit

### [L-08] Project has NPM Dependency which uses a vulnerable version : `@openzeppelin`


```js
1 result - 1 file

package.json:
  1: {
  9:     "@openzeppelin/contracts-upgradeable": "^4.4.2",
 
```

```js
VULNERABILITY	VULNERABLE VERSION
M
Incorrect Calculation	
>=4.8.0 <4.8.2
M
Incorrect Calculation	
>=4.8.0 <4.8.2
H
Improper Verification of Cryptographic Signature	
<4.7.3
M
Denial of Service (DoS)	
>=3.2.0 <4.7.2
L
Incorrect Resource Transfer Between Spheres	
>=4.6.0 <4.7.2
H
Incorrect Calculation	
>=4.3.0 <4.7.2
H
Information Exposure	
>=4.1.0 <4.7.1
H
Information Exposure	
>=4.0.0 <4.7.1
```

### Proof Of Concept
https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts-upgradeable


### Recommended Mitigation Steps
Upgrade OZ to version 4.8.0 or higher


### [L-09] Keccak Constant values should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.



```solidity
7 results - 2 files

contracts/staking/BYTES2.sol:
  36  	/// The identifier for the right to perform token burns.
  37: 	bytes32 public constant BURN = keccak256("BURN");
  38  
  39  	/// The identifier for the right to perform some contract changes.
  40: 	bytes32 public constant ADMIN = keccak256("ADMIN");
  41  

contracts/staking/NeoTokyoStaker.sol:
  205  	/// The identifier for the right to configure the LP token address.
  206: 	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
  207  
  208  	/// The identifier for the right to configure timelock options.
  209: 	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
  
  213  	/// The identifier for the right to configure Identity and Vault points.
  214: 	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
  215  
  216  	/// The identifier for the right to configure emission rates and the DAO tax.
  217: 	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
  218  
  219  	/// The identifier for the right to configure BYTES staking caps.
  220: 	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```


### [L-10] In the `constructor` , there is no return of incorrect address identification

In case of incorrect address definition in the `constructor` , there is no way to fix it because of the variables are immutable

It is recommended to fix the architecture
1- Address definitions can be done changeable architecture
2- Because of `owner = address(0)` at the end of the constructor, there is no way to fix it, so the owner's authority can be maintained.

```solidity
contracts/staking/BYTES2.sol:
  74  	*/
  75: 	constructor (
  76: 		address _bytes,
  77: 		address _s1Citizen,
  78: 		address _staker,
  79: 		address _treasury
  80: 	) {
  81: 		BYTES1 = _bytes;
  82: 		S1_CITIZEN = _s1Citizen;
  83: 		STAKER = _staker;
  84: 		TREASURY = _treasury;
  85: 	}

```

### [N-01] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters


```diff

contracts/staking/NeoTokyoStaker.sol:
  1043  	*/
  1044: 	function _stakeBytes (
  1045: 		uint256
  1046: 	) private {
  1047: 		uint256 amount;
  1048: 		uint256 citizenId;
  1049: 		uint256 seasonId;
  1050: 		assembly{
  1051: 			amount := calldataload(0x44)
  1052: 			citizenId := calldataload(0x64)
  1053: 			seasonId := calldataload(0x84)
  1054: 		}
  1055: 
  1056: 		// Attempt to transfer BYTES to escrow.
  1057: 		_assetTransferFrom(BYTES, msg.sender, address(this), amount);
  1058: 
  1059: 		// Handle staking BYTES into an S1 Citizen.
  1060: 		if (seasonId == 1) {
  1061: 			StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
  1062: 			uint256 cap = VAULT_CAP;
  1063: 			if (!citizenStatus.hasVault) {
  1064: 				cap = NO_VAULT_CAP;
  1065: 			}
  1066: 			if (citizenStatus.stakedBytes + amount > cap) {
  1067: 				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
  1068: 			}
  1069: 
  1070: 			// Validate that the caller actually staked the Citizen.
  1071: 			if (citizenStatus.timelockEndTime == 0) {
  1072: 				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
  1073: 			}
  1074: 
  1075: 			PoolData storage pool = _pools[AssetType.S1_CITIZEN];
  1076: 			unchecked {
  1077: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
  1078: 				citizenStatus.stakedBytes += amount;
  1079: 				citizenStatus.points += bonusPoints;
  1080: 				pool.totalPoints += bonusPoints;
  1081: 			}
  1082: 
  1083: 		// Handle staking BYTES into an S2 Citizen.
  1084: 		} else if (seasonId == 2) {
  1085: 			StakedS2Citizen storage citizenStatus = stakedS2[msg.sender][citizenId];
  1086: 			uint256 cap = NO_VAULT_CAP;
  1087: 			if (citizenStatus.stakedBytes + amount > cap) {
  1088: 				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
  1089: 			}
  1090: 
  1091: 			// Validate that the caller actually staked the Citizen.
  1092: 			if (citizenStatus.timelockEndTime == 0) {
  1093: 				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
  1094: 			}
  1095: 
  1096: 			PoolData storage pool = _pools[AssetType.S2_CITIZEN];
  1097: 			unchecked {
  1098: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
  1099: 				citizenStatus.stakedBytes += amount;
  1100: 				citizenStatus.points += bonusPoints;
  1101: 				pool.totalPoints += bonusPoints;
  1102: 			}
  1103: 
  1104: 		// Revert because an invalid season ID has been supplied.
  1105: 		} else {
  1106: 			revert InvalidSeasonId(seasonId);
  1107: 		}
  1108: 
  1109: 		// Emit an event.
  1110: 		emit Stake(
+			citizenId
  1111: 			msg.sender,
  1112: 			BYTES,
  1113: 			(seasonId << 128) + citizenId,
  1114: 			amount
  1115: 		);
  1116: 	}

```

### [N-02] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-03] Tokens accidentally sent to the contract cannot be recovered


**Contex**
contracts/staking/NeoTokyoStaker.sol:

It can't be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```

### [N-04] Floating pragma

**Description:**
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
https://swcregistry.io/docs/SWC-103

Floating Pragma List: 

```solidity

2 results - 2 files

contracts/staking/BYTES2.sol:
  1  // SPDX-License-Identifier: AGPL-3.0-only
  2: pragma solidity ^0.8.19;
  3  

contracts/staking/NeoTokyoStaker.sol:
  1  // SPDX-License-Identifier: AGPL-3.0-only
  2: pragma solidity ^0.8.19;
  3 
```


**Recommendation:**
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

### [N-05] Use SMTChecker

The *highest* tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

### [N-06] Constants on the left are better

If you use the constant first you support structures that veil programming errors. And one should only produce code either to add functionality, fix an programming error or trying to support structures to avoid programming errors (like design patterns). 

https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html

```solidity

4 results - 1 file

contracts/staking/NeoTokyoStaker.sol:
   910: 		if (citizenVaultId != 0 && vaultId != 0) {
   917: 		} else if (citizenVaultId != 0 && vaultId == 0) {
   926: 		} else if (citizenVaultId == 0 && vaultId != 0) {
  1834: 				if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
```


### [N-07] `constants` should be defined rather than using magic numbers

A magic number is a numeric literal that is used in the code without any explanation of its meaning. The use of magic numbers makes programs less readable and hence more difficult to maintain and update.
Even assembly can benefit from using readable constants instead of hex/numeric literals

```solidity

contracts/staking/BYTES2.sol:
  149  		*/
  150: 		uint256 treasuryShare;
  151: 		unchecked {
  152: 			treasuryShare = _amount * 2 / 3; 
  153: 		}
  154: 		_mint(TREASURY, treasuryShare);
  155  	}

contracts/staking/NeoTokyoStaker.sol:
  1076  			unchecked {
  1077: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

  1097  			unchecked {
  1098: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);


```


### [N-08] Use the `delete` keyword instead of assigning a value of `0`


Using the 'delete' keyword instead of assigning a '0' value is a detailed optimization that increases code readability and audit quality, and clearly indicates the intent.

Other hand, if use `delete` instead `0` value assign , it will be gas saved

```solidity

8 results - 1 file

contracts/staking/NeoTokyoStaker.sol:
  1517: 		stakedCitizen.stakedBytes = 0;
  1518: 		stakedCitizen.timelockEndTime = 0;
  1519: 		stakedCitizen.points = 0;
  1521: 		stakedCitizen.stakedVaultId = 0;
  1582: 		stakedCitizen.stakedBytes = 0;
  1583: 		stakedCitizen.timelockEndTime = 0;
  1584: 		stakedCitizen.points = 0;
  1635: 			lpPosition.multiplier = 0;
```


### [N-09] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity

12 results - 2 files

contracts/staking/BYTES2.sol:
  37: 	bytes32 public constant BURN = keccak256("BURN");
  40: 	bytes32 public constant ADMIN = keccak256("ADMIN");

contracts/staking/NeoTokyoStaker.sol:
  191: 	bytes4 constant private _TRANSFER_FROM_SELECTOR = 0x23b872dd;
  194: 	bytes4 constant private _TRANSFER_SELECTOR = 0xa9059cbb;
  197: 	uint256 constant private _PRECISION = 1e12;
  200: 	uint256 constant private _DIVISOR = 100;
  203: 	uint256 constant private _BYTES_PER_POINT = 200 * 1e18;
  206: 	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
  209: 	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
  214: 	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
  217: 	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
  220: 	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```

### [N-10] Function Calls in Loop Could Lead to Denial of Service due to Array length not being checked


```diff

1 result - 1 file

contracts/staking/NeoTokyoStaker.sol:
  1819: 	function configurePools (
  1820: 		PoolConfigurationInput[] memory _inputs
  1821: 	) hasValidPermit(UNIVERSAL, CONFIGURE_POOLS) external {
+                require(_inputs.length< max _inputesArrayLengt, "max length");
  1822: 		for (uint256 i; i < _inputs.length; ) {
  1823: 			uint256 poolRewardWindowCount = _inputs[i].rewardWindows.length;
  1824: 			_pools[_inputs[i].assetType].rewardCount = poolRewardWindowCount;
  1825: 			_pools[_inputs[i].assetType].daoTax = _inputs[i].daoTax;
  1826: 



```

**Recommendation:**
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to gas limitations or failed transactions. Consider bounding the loop length if the array is expected to be growing and/or handling a huge list of elements to avoid unnecessary gas wastage and denial of service.


### [N-11] According to the syntax rules, use ` => mapping (` instead of ` => mapping(` using spaces as keyword

```js
contracts/staking/NeoTokyoStaker.sol:
  280: 	mapping ( AssetType => mapping ( uint256 => uint256 )) public	timelockOptions;
  319: 	mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
  326: 	mapping ( uint256 => mapping ( string => string )) public identityCreditYield;


contracts/staking/NeoTokyoStaker.sol:
-  372: 	mapping ( address => mapping( uint256 => StakedS1Citizen )) public stakedS1;
+  372: 	mapping ( address => mapping ( uint256 => StakedS1Citizen )) public stakedS1;

- 405: 	mapping ( address => mapping( uint256 => StakedS2Citizen )) public stakedS2;
+ 405: 	mapping ( address => mapping ( uint256 => StakedS2Citizen )) public stakedS2;

```

### [N-12] For modern and more readable code; update import usages

**Context:**
```solidity

9 results - 2 files

contracts/staking/BYTES2.sol:
  4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
  5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
  7: import "../access/PermitControl.sol";
  8: import "../interfaces/IByteContract.sol";
  9: import "../interfaces/IStaker.sol";

contracts/staking/NeoTokyoStaker.sol:
  4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
  6: import "../access/PermitControl.sol";
  7: import "../interfaces/IByteContract.sol";
  8: import "../interfaces/IGenericGetter.sol";

```
**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

### [N-13] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does


This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.


```solidity
7 results - 1 file

contracts/staking/NeoTokyoStaker.sol:
   833: 		assembly {
   834: 			let length := mload(a)
   886: 		assembly {
   887: 			citizenId := calldataload(0x44)
  1001: 		assembly {
  1002: 			citizenId := calldataload(0x44)
  1236: 		assembly {
  1237: 			switch _assetType
  1461: 		assembly {
  1462: 			citizenId := calldataload(0x24)
  1536: 		assembly {
  1537: 			citizenId := calldataload(0x24)
  1682: 		assembly {
  1683: 			switch _assetType

```

### [N-14] Take advantage of Custom Error's return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can 
be written inside the `()` sign, this kind of approach provides a serious advantage in 
debugging and examining the revert details of dapps such as tenderly.

For Example;

```solidity

3 results - 1 file

contracts/staking/NeoTokyoStaker.sol:
  1149: 			revert MismatchedTimelock();
  1712: 			revert LockedConfigurationOfLP();
  1835: 			revert RewardWindowTimesMustIncrease();
```


### [N-15] Pragma version^0.8.19  version too recent to be trusted.

https://github.com/ethereum/solidity/blob/develop/Changelog.md
0.8.19 (2023-02-22)
0.8.17 (2022-09-08)
0.8.16 (2022-08-08)
0.8.15 (2022-06-15)
0.8.10 (2021-11-09)


Unexpected bugs can be reported in recent versions;
Risks related to recent releases
Risks of complex code generation changes
Risks of new language features
Risks of known bugs

Use a non-legacy and more battle-tested version
Use 0.8.10


### [N-16 ] Inconsistent Solidity Versions 

**Description:**
Different Solidity compiler versions are used ,the following contracts mix versions: 

```js
21 results - 21 files

contracts/access/PermitControl.sol:
  2: pragma solidity ^0.8.19;

contracts/interfaces/IByteContract.sol:
  2: pragma solidity ^0.8.19;

contracts/interfaces/IGenericGetter.sol:
  2: pragma solidity ^0.8.19;

contracts/interfaces/IStaker.sol:
  2: pragma solidity ^0.8.19;

contracts/s1/beckLoot.sol:
  10: pragma solidity ^0.8.0;

contracts/s1/BYTESContract.sol:
  5: pragma solidity ^0.8.0;

contracts/s1/NTCitizenDeploy.sol:
  10: pragma solidity 0.8.11;

contracts/s1/NTItems.sol:
  10: pragma solidity ^0.8.0;

contracts/s1/NTLandDeploy.sol:
  10: pragma solidity ^0.8.0;

contracts/s1/vaultBox.sol:
  10: pragma solidity ^0.8.0;

contracts/s2/NTOuterCitizenDeploy.sol:
  10: pragma solidity 0.8.11;

contracts/s2/NTOuterIdentity.sol:
  10: pragma solidity ^0.8.0;

contracts/s2/NTS2Items.sol:
  10: pragma solidity ^0.8.0;

contracts/s2/NTS2LandDeploy.sol:
  10: pragma solidity ^0.8.0;

contracts/staking/BYTES2.sol:
  2: pragma solidity ^0.8.19;

contracts/staking/NeoTokyoStaker.sol:
  2: pragma solidity ^0.8.19;

```

**Recommendation:**
Versions must be consistent with each other.

### [S-01] You can explain the operation of critical functions in NatSpec with an infographic.

An example project;
https://etherscan.io/address/0xe87a68de82204bfa63e4d626d4c5194481cf3b59#code#F1#L244

```js
/**

     *** ---------------------------------- ***
     ***                                    ***
     ***     PUBLIC MINTING FUNCTIONS       ***
     ***                                    ***
     *** ---------------------------------- ***
     ***/
    //                       ,-.
    //                       `-'
    //                       /|\
    //                        |                       ,----------.
    //                       / \                      |ERC721Drop|
    //                     Caller                     `----+-----'
    //                       |          purchase()         |
    //                       | ---------------------------->
    //                       |                             |
    //                       |                             |
    //          ___________________________________________________________
    //          ! ALT  /  drop has no tokens left for caller to mint?      !
    //          !_____/      |                             |               !
    //          !            |    revert Mint_SoldOut()    |               !
    //          !            | <----------------------------               !
    //          !~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //          !~[noop]~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //                       |                             |
    //                       |                             |
    //          ___________________________________________________________
    //          ! ALT  /  public sale isn't active?        |               !
    //          !_____/      |                             |               !
    //          !            |    revert Sale_Inactive()   |               !
    //          !            | <----------------------------               !
    //          !~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //          !~[noop]~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //                       |                             |
    //                       |                             |
    //          ___________________________________________________________
    //          ! ALT  /  inadequate funds sent?           |               !
    //          !_____/      |                             |               !
    //          !            | revert Purchase_WrongPrice()|               !
    //          !            | <----------------------------               !
    //          !~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //          !~[noop]~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //                       |                             |
    //                       |                             |----.
    //                       |                             |    | mint tokens
    //                       |                             |<---'
    //                       |                             |
    //                       |                             |----.
    //                       |                             |    | emit IERC721Drop.Sale()
    //                       |                             |<---'
    //                       |                             |
    //                       | return first minted token ID|
    //                       | <----------------------------
    //                     Caller                     ,----+-----.
    //                       ,-.                      |ERC721Drop|
    //                       `-'                      `----------'
    //                       /|\
    //                        |
    //                       / \
    /**
```
