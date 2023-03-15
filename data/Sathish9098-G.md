# GAS OPTIMIZATIONS

##

### [G-1] OPTIMIZE NAMES TO SAVE GAS

public/external function names and public member variable names can be optimized to save gas.  Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)


FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

 
      /// @audit 
      upgradeBytes(),getReward(),burn(),changeStakingContractAddress(),updateReward(),updateRewardOnMint()
      34: contract BYTES2 is PermitControl, ERC20("BYTES", "BYTES") {

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

      /// @audit getCreditYield(),getConfiguredVaultMultiplier(),getStakerPosition(),getStakerPositions(),stake(),
      getPoolReward(),claimReward(),withdraw(),configureLP(),lockLP(),configureTimelockOptions(),
      configureIdentityCreditYields(),configureIdentityCreditPoints()configureVaultCreditMultipliers(),
      configurePools(),configureCaps
     188: contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

##

### [G-2] Setting the constructor to payable

INSTANCES(2):

APROXIMATE GAS SAVED : 26 GAS 

ou can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

     75 : constructor (

[staking/BYTES2.sol#L75](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L75)

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

    588: constructor (

[NeoTokyoStaker.sol#L588](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588)

##

### [G-3] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

Using immutable variables instead of constant variables for expressions that evaluate to constant values, such as a call to keccak256(), can potentially save gas in smart contracts

The reason for this is that immutable variables are evaluated at compile time and the resulting values are hardcoded into the bytecode of the contract. This means that the gas cost of evaluating the expression is incurred only once, during contract compilation, and not at runtime

In contrast, constant variables are evaluated at runtime, which can result in additional gas costs. Specifically, when a constant variable is accessed, the EVM must perform a lookup to retrieve the value of the variable from storage, which can add to the gas cost of executing the contract

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

        37: bytes32 public constant BURN = keccak256("BURN");

        40: bytes32 public constant ADMIN = keccak256("ADMIN");

[BYTES2.sol#L37-L40](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L37-L40)

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

     206: bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

     209: bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);

     214: bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

     217: bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

    220: bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

[NeoTokyoStaker.sol#L206-L220](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L206-L220)



### [G-4] MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

    316: mapping ( AssetType => PoolData ) private _pools;

    319: mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;

    326: mapping ( uint256 => mapping ( string => string )) public identityCreditYield;

    329: mapping ( string => uint256 ) public identityCreditPoints;

    332:  mapping ( string => uint256 ) public vaultCreditMultiplier;

[NeoTokyoStaker.sol#L316-L332](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L316-L332)

    372 : mapping ( address => mapping( uint256 => StakedS1Citizen )) public stakedS1;

    378:  mapping ( address => uint256[] ) private _stakerS1Position;

[NeoTokyoStaker.sol#L372-L378](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L372-L378)

    405: mapping ( address => mapping( uint256 => StakedS2Citizen )) public stakedS2;

    411: mapping ( address => uint256[] ) private _stakerS2Position;

[NeoTokyoStaker.sol#L405-L411](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L405-L411)

   434: mapping ( address => LPPosition ) public stakerLPPosition;

[NeoTokyoStaker.sol#L434](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L434)

##

### [G-5] CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS

INSTANCES(10):

APROXIMATE GAS SAVED : 90 GAS 

In Solidity, declaring a variable inside a loop can result in higher gas costs compared to declaring it outside the loop. This is because every time the loop runs, a new instance of the variable is created, which can lead to unnecessary memory allocation and increased gas costs

On the other hand, if you declare a variable outside the loop, the variable is only initialized once, and you can reuse the same instance of the variable inside the loop. This approach can save gas and optimize the execution of your code

GAS SAMPLE TEST IN remix ide(Without gas optimizations) :

Before Mitigation :

     function testGas() public view {

        for(uint256 i = 0; i < 10; i++) {
          address collateral = msg.sender;
          address  collateral1 = msg.sender;
            
        }

The execution Cost : 2176 

After Mitigation :

     function testGas() public view {
     address collateral; address  collateral1;
        for(uint256 i = 0; i < 10; i++) {
         collateral = msg.sender;
         collateral1 = msg.sender;
            
        }

The execution Cost : 2086  

>  Hence clearly after mitigation the gas cost is reduced. Approximately its possible to save 9 gas for every iterations 

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

      for (uint256 i; i < _stakerS1Position[_staker].length; ) {
      uint256 citizenId = _stakerS1Position[_staker][i];
      StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];

[NeoTokyoStaker.sol#L717-L719](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L717-L719)

     for (uint256 i; i < _stakerS2Position[_staker].length; ) {
     uint256 citizenId = _stakerS2Position[_staker][i];
     StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];

[NeoTokyoStaker.sol#L734-L736](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L734-L736)

  
     for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
     uint256 citizenId = _stakerS1Position[_recipient][i];
     StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];

[NeoTokyoStaker.sol#L1279-L1281](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1279-L1281)

    for (uint256 i; i < _stakerS2Position[_recipient].length; ) {
    uint256 citizenId = _stakerS2Position[_recipient][i];
    StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];

[NeoTokyoStaker.sol#L1288-L1290](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1288-L1290)

    for (uint256 i; i < windowCount; ) {
    RewardWindow memory window = pool.rewardWindows[i];

[NeoTokyoStaker.sol#L1312-L1313](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1312-L1313)

      1320 : uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;

[NeoTokyoStaker.sol#L1320](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1320)

      1331: uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;

[NeoTokyoStaker.sol#L1331](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1331)

      1342: uint256 timeSinceReward = window.startTime - lastPoolRewardTime;

[NeoTokyoStaker.sol#L1342](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1342)

##

### [G-6] Use nested if and, avoid multiple check combinations

For each && split can save 10-20 gas

INSTANCES(10):

APROXIMATE GAS SAVED : 100 GAS 

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

      910: if (citizenVaultId != 0 && vaultId != 0) {

[NeoTokyoStaker.sol#L910] (https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L910)

      917: } else if (citizenVaultId != 0 && vaultId == 0) {

[NeoTokyoStaker.sol#L917](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L917)

      926: } else if (citizenVaultId == 0 && vaultId != 0) {

[NeoTokyoStaker.sol#L926](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L926)

##

### [G-7] INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT


FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

        37: bytes32 public constant BURN = keccak256("BURN");

        40: bytes32 public constant ADMIN = keccak256("ADMIN");

[BYTES2.sol#L37-L40](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L37-L40)

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

     206: bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

     209: bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);

     214: bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

     217: bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

     220: bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

[NeoTokyoStaker.sol#L206-L220](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L206-L220)

##

### [G-8] <x> += <y> OR <x>-=<y> costs more gas than <x> = <x> + <y> OR  <x> = <x> - <y>   for state variables

Using the addition/Subtraction operator instead of plus-equals/minus-equals saves 113 gas

INSTANCES(13):

APROXIMATE GAS SAVED : 1469 GAS 

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

      977: pool.totalPoints += citizenStatus.points;

[NeoTokyoStaker.sol#L977](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L977)

      1029 : pool.totalPoints += citizenStatus.points;

[NeoTokyoStaker.sol#L1029](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1029)

     1078: citizenStatus.stakedBytes += amount;
     1079: citizenStatus.points += bonusPoints;
     1080: pool.totalPoints += bonusPoints;

[NeoTokyoStaker.sol#L1078-L1080](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1078-L1080)

     1160: stakerLPPosition[msg.sender].amount += amount;
     1161: stakerLPPosition[msg.sender].points += points;

     1164: pool.totalPoints += points;

[NeoTokyoStaker.sol#L1160-L1164](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1160-L1164)

     1519 : pool.totalPoints -= stakedCitizen.points;

[NeoTokyoStaker.sol#L1515](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1515)

     1580 : pool.totalPoints -= stakedCitizen.points;

[NeoTokyoStaker.sol#L1580](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1580)

     1626: lpPosition.amount -= amount;
     1627: lpPosition.points -= points;
     1630: pool.totalPoints -= points;

[NeoTokyoStaker.sol#L1626-L1627](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1626-L1627)

  

##

### [G-9] Use constants instead of type(uintx).max

INSTANCES(3):

Using constants instead of type(uintx).max can save gas in some cases because constants are precomputed at compile-time and can be reused throughout the code, whereas type(uintx).max requires computation at runtime

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

  963:  uint256 timelockMultiplier = _timelock & type(uint128).max;

  1017 : uint256 timelockMultiplier = _timelock & type(uint128).max;

[NeoTokyoStaker.sol#L1017](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1017)

  1141 : uint256 timelockMultiplier = _timelock & type(uint128).max;

[NeoTokyoStaker.sol#L1141](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1141)

##

### [G-10] Use assembly to write address storage values

INSTANCES(10):

         598: BYTES = _bytes;
         599: S1_CITIZEN = _s1Citizen;
         600: S2_CITIZEN = _s2Citizen;
         601: LP = _lpToken;
         602: IDENTITY = _identity;
         603: VAULT = _vault;

[NeoTokyoStaker.sol#L598](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L598)

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

     81: BYTES1 = _bytes;
     82: S1_CITIZEN = _s1Citizen;
     83: STAKER = _staker;
     84: TREASURY = _treasury;

[BYTES2.sol#L81-L84](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L81-L84)

##

### [G-11] Use calldata instead of memory for function arguments that do not get mutated

INSTANCES(2):

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

       824: function _stringEquals (
		string memory _a,
		string memory _b
           ) private pure returns (bool) {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L824-L865)

##

### [G-12] Save gas with the use of the import statement

INSTANCES(2):

While the following two critical VAULT_CAP,NO_VAULT_CAP values are assigned in the constructor, there is no zero value control. This means that if both state variables are started with a possible value of 0, the contract must be deployed again. This possibility means gas consumption.

Zero value control is the most error-prone value control since zero value is assigned in case of no value entry due to EVM design.

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

     604: VAULT_CAP = _vaultCap;
     605: NO_VAULT_CAP = _noVaultCap;

[NeoTokyoStaker.sol#L604-L605](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L604-L605)

Recommendation:

It is recommended to perform a zero value check for critical value assignments.

##

### [G-13] Using delete instead of setting struct 0 saves gas

INSTANCES(8):

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

         1517: stakedCitizen.stakedBytes = 0;
         1518: stakedCitizen.timelockEndTime = 0;
         1519: stakedCitizen.points = 0;
         1520: stakedCitizen.hasVault = false;
         1521: stakedCitizen.stakedVaultId = 0;

[NeoTokyoStaker.sol#L1517-L1521](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1517-L1521)

       1582: stakedCitizen.stakedBytes = 0;
       1583: stakedCitizen.timelockEndTime = 0;
       1584: stakedCitizen.points = 0;

[NeoTokyoStaker.sol#L1582-L1584](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1582-L1584)

## 

### [G-14] Gas overflow during iteration (DoS)

Each iteration of the cycle requires a gas flow. A moment may come when more gas is required than it is allocated to record one block. In this case, all iterations of the loop will fail

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L717-L728)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L734-L743)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1279-L1302)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1312-L1383)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1499-L1509)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1564-L1574)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1742-L1745)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1765-L1772)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1787-L1791)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1806-L1809)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1822-L1840)Reco

Recommendations:

Check the maxProcessingLengt allowed before start iterating the loops

 require(Varibale.length < maxProcessingLengt, "max length");

## [G-15] Save gas with the use of the import statement

INSTANCES(8):

Description:

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.

This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.

Recommendation:

import {contract1 , contract2} from "filename.sol";

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

  4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

  5: import "../access/PermitControl.sol";
  6: import "../interfaces/IByteContract.sol";
  7: import "../interfaces/IGenericGetter.sol";

[NeoTokyoStaker.sol#L4-L8](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L4-L8)

 FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

   import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
   import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

   import "../access/PermitControl.sol";
   import "../interfaces/IByteContract.sol";
   import "../interfaces/IStaker.sol";

[NeoTokyoStaker.sol#L4-L8](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L4-L8)

##

### [G-16] Avoid contract existence checks by using solidity version 0.8.10 or later

INSTANCES(5):

 FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

               (
			uint256 reward,
			uint256 daoCommision
		) = IStaker(STAKER).claimReward(_to);

       96: if (IERC20(BYTES1).balanceOf(msg.sender) < _amount) {

[BYTES2.sol#L117-L120](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L117-L120)

 FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol  

   632: uint256 rewardRate = citizen.getRewardRateOfTokenId(_citizenId);

[NeoTokyoStaker.sol#L632](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L632)

    904: uint256 citizenVaultId = citizen.getVaultIdOfTokenId(citizenId);
   
    948: uint256 identityId = citizen.getIdentityIdOfTokenId(citizenId);

##

### [G-17] Make 3 event parameters indexed when possible

INSTANCES(4):

It’s the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

 FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

The same extra indexed parameter can be applied to BytesUpgraded()

      event BytesUpgraded (
		address indexed caller,
		uint256 amount
	);

[BYTES2.sol#L61-L64](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L61-L64)

 FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol 

The same extra indexed parameter can be applied to Stake()

        event Stake (
		address indexed staker,
		address indexed asset,
		uint256 timelockOption,
		uint256 amountOrTokenId
	);

[NeoTokyoStaker.sol#L539-L544](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L539-L544)

The same extra indexed parameter can be applied to Claim()

       event Claim (
		address indexed recipient,
		uint256 reward,
		uint256 tax
	);

[NeoTokyoStaker.sol#L553-L557](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L553-L557)

The same extra indexed parameter can be applied to Withdraw() 

     event Withdraw (
		address indexed caller,
		address indexed asset,
		uint256 amountOrTokenId
	);

[NeoTokyoStaker.sol#L567-L571](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L567-L571)

##

### [G-18] Duplicated if() checks should be refactored to a modifier or function

INSTANCES(12):

 FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol 

   693:  if (_assetType == AssetType.S1_CITIZEN) {
   1278: if (_assetType == AssetType.S1_CITIZEN) {

 [NeoTokyoStaker.sol#L693](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L693),[NeoTokyoStaker.sol#L1278](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1278)

   1287: 	} else if (_assetType == AssetType.S2_CITIZEN) {
   695:         } else if (_assetType == AssetType.S2_CITIZEN) {

[NeoTokyoStaker.sol#L1287](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1287),[NeoTokyoStaker.sol#L695](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L695)

   1071: if (citizenStatus.timelockEndTime == 0) {
   1092: if (citizenStatus.timelockEndTime == 0) {

[NeoTokyoStaker.sol#L1071](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1071),[NeoTokyoStaker.sol#L1092](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1092)

   1472: if (stakedCitizen.timelockEndTime == 0) {
   1547: if (stakedCitizen.timelockEndTime == 0) {

[NeoTokyoStaker.sol#L1472](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1472),[NeoTokyoStaker.sol#L1547](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1547)

   1477: if (stakedCitizen.stakedBytes > 0) {
   1552: if (stakedCitizen.stakedBytes > 0) {

[NeoTokyoStaker.sol#L1477](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1477),[NeoTokyoStaker.sol#L1552](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1552)

   1502: if (citizenId == oldPosition[stakedIndex]) {
   1567: if (citizenId == oldPosition[stakedIndex]) {

[NeoTokyoStaker.sol#L1502](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1502),[NeoTokyoStaker.sol#L1567](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1567)

##
##

### [G-19] Create the instance of IGenericGetter(VAULT),IGenericGetter(S1_CITIZEN) outside the functions . We can reuse the instance variable whenever needed instead of creating new instance variables every time . This will save the gas cost 

INSTANCES(2):

 FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol 

   638: IGenericGetter vault = IGenericGetter(VAULT);

   664: IGenericGetter vault = IGenericGetter(VAULT);

[NeoTokyoStaker.sol#L638](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L638),[NeoTokyoStaker.sol#L664](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L664)

   631: IGenericGetter citizen = IGenericGetter(S1_CITIZEN);
   903: IGenericGetter citizen = IGenericGetter(S1_CITIZEN);
   

[NeoTokyoStaker.sol#L631](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L631),[NeoTokyoStaker.sol#L903](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L903)

##

### [G-20] Cache the S1_CITIZEN immutable address variable value with local address variable . This will save the 60 gas as per remix gas reports 

PROOF OF CONCEPT (REMIX WITHOUT OPTIMIZATIONS) :

pragma solidity ^0.8.7;

contract MappingTest {

address public immutable A=0xE0f5206BBD039e7b0592d8918820024e2a7437b9;
// execution cost 195 gas
function withoutImmutableCache() public view {
    if(A==0xE0f5206BBD039e7b0592d8918820024e2a7437b9){
        if(A==0xE0f5206BBD039e7b0592d8918820024e2a7437b9){

        }
    }
}
// execution cost 135 gas
function withImmutableCache() public view {

    address a=A;
     if(a==0xE0f5206BBD039e7b0592d8918820024e2a7437b9){
         if(a==0xE0f5206BBD039e7b0592d8918820024e2a7437b9){

        }
    }
}

}

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol  

  897: _assetTransferFrom(S1_CITIZEN, msg.sender, address(this), citizenId);

  903: IGenericGetter citizen = IGenericGetter(S1_CITIZEN);

     emit Stake(
     msg.sender,
     S1_CITIZEN,
     _timelock,
     citizenId
    );

[NeoTokyoStaker.sol#L897](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L897),[NeoTokyoStaker.sol#L903](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L903),[NeoTokyoStaker.sol#L983](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L983)

> S2_CITIZEN should be cached with local address variable

  1010: _assetTransferFrom(S2_CITIZEN, msg.sender, address(this), citizenId);

    emit Stake(
			msg.sender,
			S2_CITIZEN,
			_timelock,
			citizenId
		);

[NeoTokyoStaker.sol#L1010](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1010),[NeoTokyoStaker.sol#L1035](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1035)
   
##

### [G-21] Cache _PRECISION constant variable with local uint256 variable instead of calling multiple times . This will save the 9 gas as per remix gas reports

PROOF OF CONCEPT (REMIX) :

pragma solidity ^0.8.7;

contract MappingTest {
uint256 public constant A=100;
// execution cost 802 gas
function withoutContantCache() public pure {
   uint256 b=200;

   b+=A;
   b/=A;
   b*=A;
}
// execution cost 793 gas
function withConstantCache() public pure {
uint256 b=200;
uint256 c=A;
 b+=c;
   b/=c;
   b*=c;
}
}

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol  

    1388: uint256 share = points * _PRECISION / pool.totalPoints * totalReward;

    1390: share /= _PRECISION;

    1391: daoShare /= _PRECISION;

[NeoTokyoStaker.sol#L1388-L1391](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1388-L1391)

 
### [G-21] Structs can be packed into fewer storage slots

APROXIMATE GAS SAVED : 40000 (2 SLOTS)

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol  

     struct StakedS1Citizen {
       uint256 stakedBytes; //32-SLOT 1
       uint256 timelockEndTime; //32- SLOT 2
       uint256 points; //32 SLOT-3
       uint256 stakedVaultId; //32 SLOT-4
       bool hasVault; //1 SLOT-5
	}

Total slots 5

[NeoTokyoStaker.sol#L359-L365](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L359-L365)

We can declare timelockEndTime uint128 instead of uint256. Using uint128 we can store 10,774,146,971,163 years this is more than enough for timelockEndTime . So we can save 1 slot and saves 20000 gas 

Recommended Mitigation:

      /// @audit Variable ordering with 4 slots instead of the current 5:

       struct StakedS1Citizen {
       uint256 stakedBytes; //32-SLOT 1
    -  uint256 timelockEndTime; 
       uint256 points; //32 SLOT-2
       uint256 stakedVaultId; //32 SLOT-3
   +   uint128 timelockEndTime; //16- SLOT 4
       bool hasVault; //1 SLOT-4
	}

## Same like last example we can reduce one slot 

        struct StakedS1CitizenOutput {
		uint256 citizenId; //32 -SLOT1
		uint256 stakedBytes; //32 -SLOT2
		uint256 timelockEndTime; //32 -SLOT3
		uint256 points; //32 -SLOT4
		uint256 stakedVaultId; //32 -SLOT5
		bool hasVault; //1 -SLOT6
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L462-L469)

Total slots 6

Recommended Mitigation:

       /// @audit Variable ordering with 5 slots instead of the current 6:
        struct StakedS1CitizenOutput {
		uint256 citizenId; //32 -SLOT1
		uint256 stakedBytes; //32 -SLOT2
           -    uint256 timelockEndTime; 
		uint256 points; //32 -SLOT3
		uint256 stakedVaultId; //32 -SLOT4
            +   uint128 timelockEndTime; //16 -SLOT5 
		bool hasVault; //1 -SLOT5
	}

     
## 

### [G-22] State variables can be packed into fewer storage slots

INSTANCES(1):

APROXIMATE GAS SAVED : 20000 (1 SLOT)

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper


FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol  


    232: address public LP; //20 bytes

    511: bool public lpLocked; //1byte

[NeoTokyoStaker.sol#L232](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L232),[NeoTokyoStaker.sol#L511](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L511)

Move the  (bool public lpLocked) bellow the (address public LP) this wills save the one slot 


    232: address public LP; //20 bytes
  + 233: bool public lpLocked;
  - 511: bool public lpLocked;

Once moved possible to pack both variable in single slot instead of 2 slots . So possible to save 20000 gas

## 

### [G-23] PRIVATE FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

INSTANCES(5):

APROXIMATE GAS SAVED : 200 GAS 

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol  

   function _stringEquals (
		string memory _a,
		string memory _b
    ) private pure returns (bool) {

[NeoTokyoStaker.sol#L824-L827](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L824-L827)

     function _stakeS1Citizen (
		uint256 _timelock
	) private {

[NeoTokyoStaker.sol#L875-L877](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L875-L877)

     function _stakeS2Citizen (
		uint256 _timelock
	) private {

[NeoTokyoStaker.sol#L995-L998](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L995-L998)

    function _stakeBytes (
		uint256
	) private {

[NeoTokyoStaker.sol#L1044-L1046](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1044-L1046)

    function _stakeLP (
		uint256 _timelock
	) private {

[NeoTokyoStaker.sol#L1124-L1126](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1124-L1126)

##

### [G-24] Empty blocks should be removed to save deployment cost 

INSTANCES(2):

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

   function updateRewardOnMint (
		address,
		uint256
	) external {
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L204-L208)

     function updateReward (
		address,
		address,
		uint256
	) external {
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L189-L194)

##

### [G-25] Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement

INSTANCES(6):

windowCount - 1 is not possible to overflow . We already checked the value in for loop 

       1353: if (j == windowCount - 1) {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1353)

     1376: } else if (i == windowCount - 1) {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1376)

    1503: if (stakedIndex != oldPosition.length - 1) {
    1504: oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1503)

     1568: if (stakedIndex != oldPosition.length - 1) {
     1569: oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1]; 

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1568-L1569)

   
   





 

  



   







  







