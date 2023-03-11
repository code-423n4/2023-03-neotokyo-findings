### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] |Use hardcode address instead ``address(this)``| 7 |
| [G-02] |Pack state variables| 1 |
| [G-03] |Gas overflow during iteration (DoS) |1 |
| [G-04] |Using ``delete``  instead of setting struct ``0`` saves gast | 8 |
| [G-05] |Multiple ``address``/ID mappings can be combined into a single ``mapping`` of an ``address``/ID to a ``struct``, where appropriate |2|
| [G-06] |Multiple accesses of a mapping/array should use a local variable caches| 2 |
| [G-07] |Use calldata instead of memory for function arguments that do not get mutated| 4 |
| [G-08] | Missing `zero-address` check in `constructor`| 10 |
| [G-09] | _Functions guaranteed to revert_ when called by normal users can be marked ``payable`` |11 |
| [G-10] |Do not calculate constants variables1 | 7 |
| [G-11] |Use ``assembly`` to write _address storage values_  |6 |
| [G-12] |Setting the _constructor_ to `payable`| 2 |
| [G-13] |Use nested if and, avoid multiple check combinations | 2 |
| [G-14] |Optimize names to save gas|All contracts |
| [G-15] |Upgrade Solidity's optimizer |  |

Total 15 issues

### [G-01] Use hardcode address instead ``address(this)``

Instead of ``address(this)``, it is more gas-efficient to pre-calculate and use the address with a hardcode. Foundry's ``script.sol`` and solmate``LibRlp.sol`` contracts can do this.

Reference:
https://book.getfoundry.sh/reference/forge-std/compute-create-address
https://twitter.com/transmissions11/status/1518507047943245824


7 results - 1 file:
```solidity
contracts\staking\NeoTokyoStaker.sol:

   897:    _assetTransferFrom(S1_CITIZEN, msg.sender, address(this), citizenId);

   927: 	_assetTransferFrom(VAULT, msg.sender, address(this), vaultId);

  1010: 	_assetTransferFrom(S2_CITIZEN, msg.sender, address(this), citizenId);

  1057: 	_assetTransferFrom(BYTES, msg.sender, address(this), amount);

  1137: 	_assetTransferFrom(LP, msg.sender, address(this), amount);

  1492: 	_assetTransferFrom(S1_CITIZEN, address(this), msg.sender, citizenId);

  1557: 	_assetTransferFrom(S2_CITIZEN, address(this), msg.sender, citizenId);

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L897


### [G-02] Pack state variables

State variable packaging in ** NeoTokyoStaker.sol ** contract  (**1 * 2k = 2k gas saved**)

```solidity
contracts\staking\NeoTokyoStaker.sol:

  231: 	/// The address of the LP token contract.
  232: 	address public LP;
  233 
  234 	/**
  235 	The address of the Neo Tokyo S1 Identity contract. This specific contract 
  236 	address is stored to check an assembled S1 Citizen's specific component 
  237 	identity in order to check for Hands of the Citadel.
  238 	*/
  239 	address immutable public IDENTITY;



  510: 	/// Whether or not setting the LP token contract address is locked.
  511: 	bool public lpLocked;

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L511


```diff
contracts\staking\NeoTokyoStaker.sol:

  231: 	/// The address of the LP token contract.
  232: 	address public LP;
+ 510: 	/// Whether or not setting the LP token contract address is locked.
+ 511: 	bool public lpLocked; 
  233 
  234 	/**
  235 	The address of the Neo Tokyo S1 Identity contract. This specific contract 
  236 	address is stored to check an assembled S1 Citizen's specific component 
  237 	identity in order to check for Hands of the Citadel.
  238 	*/
  239 	address immutable public IDENTITY;



- 510: 	/// Whether or not setting the LP token contract address is locked.
- 511: 	bool public lpLocked;

```


### [G-03] Gas overflow during iteration (DoS)

Each iteration of the cycle requires a gas flow. A moment may come when more gas is required than it is allocated to record one block. In this case, all iterations of the loop will fail.

1 result - 1 file:
```diff
contracts\staking\NeoTokyoStaker.sol:

  1819: 	function configurePools (
  1820: 		PoolConfigurationInput[] memory _inputs
  1821: 	) hasValidPermit(UNIVERSAL, CONFIGURE_POOLS) external {
+              require(_inputs.length< max _inputsLengt, "max length");

  1822: 	for (uint256 i; i < _inputs.length; ) {
  1823: 		uint256 poolRewardWindowCount = _inputs[i].rewardWindows.length;
  1824: 		_pools[_inputs[i].assetType].rewardCount = poolRewardWindowCount;
  1825: 		_pools[_inputs[i].assetType].daoTax = _inputs[i].daoTax;
  1826: 
  1827: 		// Set the pool reward window details by populating the mapping.
  1828: 		uint256 lastTime;
  1829: 		for (uint256 j; j < poolRewardWindowCount; ) {
  1830: 			_pools[_inputs[i].assetType].rewardWindows[j] =
  1831: 				_inputs[i].rewardWindows[j];
  1832: 
  1833: 			// Revert if an invalid pool configuration is supplied.
  1834: 			if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
  1835: 				revert RewardWindowTimesMustIncrease();
  1836: 			}
  1837: 			lastTime = _inputs[i].rewardWindows[j].startTime;
  1838: 			unchecked { j++; }
  1839: 		}
  1840: 		unchecked { ++i; }
  1841: 	}
  1842: }

  ```

Here are the data available in the covered contracts. The use of this situation in contracts that are not covered will also provide gas optimization.


### [G-04] Using ``delete``  instead of setting struct ``0`` saves gas

8 results - 1 file:
```solidity
contracts\staking\NeoTokyoStaker.sol:

  1517:    stakedCitizen.stakedBytes = 0;

  1518: 	stakedCitizen.timelockEndTime = 0;

  1519: 	stakedCitizen.points = 0;

  1520  	stakedCitizen.hasVault = false;

  1521: 	stakedCitizen.stakedVaultId = 0;

  1582: 	stakedCitizen.stakedBytes = 0;

  1583: 	stakedCitizen.timelockEndTime = 0;

  1584: 	stakedCitizen.points = 0;

  1635: 	lpPosition.multiplier = 0;

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517-L1521


**Recommendation code:**
```diff
contracts\staking\NeoTokyoStaker.sol:

- 1517:    stakedCitizen.stakedBytes = 0;
+ 1517:    delete stakedCitizen.stakedBytes;

```

### [G-05] Multiple ``address``/ID mappings can be combined into a single ``mapping`` of an ``address``/ID to a ``struct``, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.


```solidity
contracts\staking\NeoTokyoStaker.sol:
 
  328: 	/// Assign a configurable multiplier to each S1 Citizen's Identity credit. 
  329: 	mapping ( string => uint256 ) public identityCreditPoints;
  330: 
  331: 	/// Assign a configurable multiplier to each S1 Citizen's Vault credit. 
  332: 	mapping ( string => uint256 ) public vaultCreditMultiplier;

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L329-L332

### [G-06] Multiple accesses of a mapping/array should use a local variable cache

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata.

2 results - 1 file:
```solidity
contracts\staking\NeoTokyoStaker.sol:

  // @audit stakerLPPosition[msg.sender]
  1144: 		if (stakerLPPosition[msg.sender].multiplier == 0) {
  1145: 			stakerLPPosition[msg.sender].multiplier = timelockMultiplier;

  1148: 		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) {

  1158: 			stakerLPPosition[msg.sender].timelockEndTime =

  1160: 			stakerLPPosition[msg.sender].amount += amount;
  1161: 			stakerLPPosition[msg.sender].points += points;


  // @audit _pools[_inputs[i].assetType]
  1824: 			_pools[_inputs[i].assetType].rewardCount = poolRewardWindowCount;
  1825: 			_pools[_inputs[i].assetType].daoTax = _inputs[i].daoTax;

  1830: 				_pools[_inputs[i].assetType].rewardWindows[j] =

```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1144-L1161


### [G-07] Use calldata instead of memory for function arguments that do not get mutated

Mark data types as ``calldata`` instead of ``memory`` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as ``calldata``. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies ``memory`` storage.

4 results - 1 file:
```solidity
contracts\staking\NeoTokyoStaker.sol:

  // @audit _timelockIds, _encodedSetting 
  1737: 	function configureTimelockOptions (
  1738: 		AssetType _assetType,
  1739: 		uint256[] memory _timelockIds,
  1740: 		uint256[] memory _encodedSettings
  1741: 	) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {


  // @audit _citizenRewardRates, _vaultRewardRates, _identityCreditYields
  1760: 	function configureIdentityCreditYields (
  1761: 		uint256[] memory _citizenRewardRates, 
  1762: 		string[] memory _vaultRewardRates,
  1763: 		string[] memory _identityCreditYields
  1764: 	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {


  // @audit _identityCreditYields, _points
  1783: 	function configureIdentityCreditPoints (
  1784: 		string[] memory _identityCreditYields,
  1785: 		uint256[] memory _points
  1786: 	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {


  // @audit _vaultCreditMultipliers, _multipliers
  1802: 	function configureVaultCreditMultipliers (
  1803: 		string[] memory _vaultCreditMultipliers,
  1804: 		uint256[] memory _multipliers
  1805: 	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1739-L1740


###  [G-08] Missing `zero-address` check in `constructor`

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly. It also wast gas as it requires the redeployment of the contract.

10 results - 2 files:
```solidity
contracts\staking\BYTES2.sol:

  75 	constructor (
  76: 		address _bytes,
  77: 		address _s1Citizen,
  78: 		address _staker,
  79: 		address _treasury
  80 	) {
  81 		BYTES1 = _bytes;
  82 		S1_CITIZEN = _s1Citizen;
  83 		STAKER = _staker;
  84 		TREASURY = _treasury;
  85 	}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-L85


```solidity
contracts\staking\NeoTokyoStaker.sol:

  588 	constructor (
  589: 		address _bytes,
  590: 		address _s1Citizen,
  591: 		address _s2Citizen,
  592: 		address _lpToken,
  593: 		address _identity,
  594: 		address _vault,
  595 		uint256 _vaultCap,
  596 		uint256 _noVaultCap
  597 	) {
  598 		BYTES = _bytes;
  599 		S1_CITIZEN = _s1Citizen;
  600 		S2_CITIZEN = _s2Citizen;
  601 		LP = _lpToken;
  602 		IDENTITY = _identity;
  603 		VAULT = _vault;
  604 		VAULT_CAP = _vaultCap;
  605 		NO_VAULT_CAP = _noVaultCap;
  606 	}

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L606


### [G-09] _Functions guaranteed to revert_ when called by normal users can be marked ``payable``

If a function modifier such as ``onlyOwner`` is used, the function will revert if a normal user tries to pay the function. Marking the function as ``payable`` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

11 results - 2 files:
```solidity
contracts\staking\BYTES2.sol:

  140: 	function burn (
  143: 	) hasValidPermit(UNIVERSAL, BURN) external {


  162: 	function changeStakingContractAddress (
  164: 	) hasValidPermit(UNIVERSAL, ADMIN) external {


  173: 	function changeTreasuryContractAddress (
  175: 	) hasValidPermit(UNIVERSAL, ADMIN) external {

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L140-L143


```solidity
contracts\staking\NeoTokyoStaker.sol:

  1708: 	function configureLP (
  1709: 		address _lp
  1710: 	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {


  1721: 	function lockLP () external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {


  1737: 	function configureTimelockOptions (
  1738: 		AssetType _assetType,
  1739: 		uint256[] memory _timelockIds,
  1740: 		uint256[] memory _encodedSettings
  1741: 	) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {


  1760: 	function configureIdentityCreditYields (
  1761: 		uint256[] memory _citizenRewardRates, 
  1762: 		string[] memory _vaultRewardRates,
  1763: 		string[] memory _identityCreditYields
  1764: 	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {


  1783: 	function configureIdentityCreditPoints (
  1784: 		string[] memory _identityCreditYields,
  1785: 		uint256[] memory _points
  1786: 	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {


  1802: 	function configureVaultCreditMultipliers (
  1803: 		string[] memory _vaultCreditMultipliers,
  1804: 		uint256[] memory _multipliers
  1805: 	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
 

  1819: 	function configurePools (
  1820: 		PoolConfigurationInput[] memory _inputs
  1821: 	) hasValidPermit(UNIVERSAL, CONFIGURE_POOLS) external {


  1851: 	function configureCaps (
  1852: 		uint256 _vaultedCap,
  1853: 		uint256 _unvaultedCap
  1854: 	) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external {

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1708-L1710


### [G-10] Do not calculate constants variables

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

7 results - 2 files:
```solidity
contracts\staking\BYTES2.sol:

  37: 	bytes32 public constant BURN = keccak256("BURN");

  40: 	bytes32 public constant ADMIN = keccak256("ADMIN");

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37-L40


```solidity
contracts\staking\NeoTokyoStaker.sol:

  206: 	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

  209: 	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
  210: 		"CONFIGURE_TIMELOCKS"
  211: 	);

  214: 	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

  217: 	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

  220: 	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206-L220


**Recommendation Code:**
```diff
contracts\staking\BYTES2.sol:

- 37: 	bytes32 public constant BURN = keccak256("BURN");
+ // BURN = keccak256("BURN")
+ 37: 	bytes32 public constant BURN = 0x04c6a47ae7910ef8b295215a97e8495a9eaf57b7b05bfd8bf951edb3fd4a16a3;

```

### [G-11] Use ``assembly`` to write _address storage values_ 

6 results - 2 files:
```solidity
contracts\staking\NeoTokyoStaker.sol:

  588 	constructor (
  601: 		LP = _lpToken;


  1708 	function configureLP (
  1714: 		LP = _lp;

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L601


```solidity
contracts\staking\BYTES2.sol:

  75  	constructor (
  83: 		STAKER = _staker;
  84: 		TREASURY = _treasury;


  162 	function changeStakingContractAddress (
  165: 		STAKER = _staker;


  173 	function changeTreasuryContractAddress (
  176: 		TREASURY = _treasury;

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L83-L84


**Recommendation Code:**
```diff

Ethos-Vault\contracts\abstract\ReaperBaseStrategyv4.sol:
 
contracts\staking\NeoTokyoStaker.sol:

  588 	constructor (
- 601: 		LP = _lpToken;
+                  assembly {                      
+                      sstore(LP.slot,_lpToken)
+                  }                               
          
```

### [G-12] Setting the _constructor_ to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

2 results - 2 files:
```solidity
contracts\staking\BYTES2.sol:

  75: 	constructor (

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75


```solidity
contracts\staking\NeoTokyoStaker.sol:

  588: 	constructor (

```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588


**Recommendation:**
Set the constructor to ``payable``


### [G-13] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

2 results - 1 file:
```solidity
contracts\staking\NeoTokyoStaker.sol:

   910: 		if (citizenVaultId != 0 && vaultId != 0) {

  1834: 		if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {

```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L910


### [G-14] Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ```NeoTokyoStaker.sol``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

NeoTokyoStaker.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
dd6fea0b  =>  getCreditYield(uint256,uint256)
00a90e85  =>  getConfiguredVaultMultiplier(uint256)
c7e60b89  =>  getStakerPosition(address,AssetType)
667a5154  =>  getStakerPositions(address)
64c7a271  =>  _assetTransferFrom(address,address,address,uint256)
0ec126ff  =>  _assetTransfer(address,address,uint256)
339eeafe  =>  _stringEquals(string,string)
3fb2cab9  =>  _stakeS1Citizen(uint256)
b8cacd68  =>  _stakeS2Citizen(uint256)
c1a65cc5  =>  _stakeBytes(uint256)
5228f0bb  =>  _stakeLP(uint256)
c6eb5fe6  =>  stake(AssetType,uint256,uint256,uint256,uint256)
e1e6c3ff  =>  getPoolReward(AssetType,address)
d279c191  =>  claimReward(address)
93af1029  =>  _withdrawS1Citizen()
abed43f3  =>  _withdrawS2Citizen()
0a7db0a8  =>  _withdrawLP()
2f01a366  =>  withdraw(AssetType,uint256)
8b595c38  =>  configureLP(address)
763d7353  =>  lockLP()
4a29c67f  =>  configureTimelockOptions(AssetType,uint256[],uint256[])
213e9b46  =>  configureIdentityCreditYields(uint256[],string[],string[])
fab17133  =>  configureIdentityCreditPoints(string[],uint256[])
c8257634  =>  configureVaultCreditMultipliers(string[],uint256[])
02ddc99d  =>  configurePools(PoolConfigurationInput[])
7f7a0431  =>  configureCaps(uint256,uint256)

```
### [G-15] Upgrade Solidity's optimizer

Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.

```js
hardhat.config.js:
  25: module.exports = {
  26:    solidity: {
  27: 	    compilers: [
  28: 		{
  29: 		version: '0.8.11',
  30: 		settings: {
  31: 		  optimizer: {
  32: 			enabled: true,
  33: 			runs: 200, 
  34: 			details: {
  35: 			   yul: true 
  36: 			}
  37: 		    }
  38: 		}
  39: 	    },
  40: 	   {
  41: 	     version: '0.8.19',
  42: 	     settings: {
  43: 		optimizer: {
  44: 		    enabled: true,
  45: 			runs: 200, 
  46: 			details: {
  47: 			  yul: true 
  48: 			}
  49: 		      }
  50: 		  }
  51: 	     }
  52: 	   ]
  53:    },
```