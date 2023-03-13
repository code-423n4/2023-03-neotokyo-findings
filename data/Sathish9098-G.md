# GAS OPTIMIZATIONS

##

### [G-1] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

it is recommended to use the immutable keyword for expressions that result in constant values, such as a call to keccak256(), instead of using the constant keyword. This is because using immutable can lead to gas savings during contract deployment and function calls

By using immutable for constant value expressions, we can reduce the amount of gas needed to deploy the contract and execute its functions

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

     37: bytes32 public constant BURN = keccak256("BURN");

     40: bytes32 public constant ADMIN = keccak256("ADMIN");

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L37-L40)

##

### [G-2] OPTIMIZE NAMES TO SAVE GAS

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

### [G-3] Setting the constructor to payable

ou can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

   75 : constructor (

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L75)

##

### [G-5] ]  EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

Using immutable variables instead of constant variables for expressions that evaluate to constant values, such as a call to keccak256(), can potentially save gas in smart contracts

The reason for this is that immutable variables are evaluated at compile time and the resulting values are hardcoded into the bytecode of the contract. This means that the gas cost of evaluating the expression is incurred only once, during contract compilation, and not at runtime

In contrast, constant variables are evaluated at runtime, which can result in additional gas costs. Specifically, when a constant variable is accessed, the EVM must perform a lookup to retrieve the value of the variable from storage, which can add to the gas cost of executing the contract

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

        37: bytes32 public constant BURN = keccak256("BURN");

        40: bytes32 public constant ADMIN = keccak256("ADMIN");

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L37-L40)

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

   206: bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

   209: bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);

   214: bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

   217: bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

   220: bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L206-L220)



### [G-4] MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

  316: mapping ( AssetType => PoolData ) private _pools;

  319: mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;

  326: mapping ( uint256 => mapping ( string => string )) public identityCreditYield;

  329: mapping ( string => uint256 ) public identityCreditPoints;

  332: 	mapping ( string => uint256 ) public vaultCreditMultiplier;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L316-L332)

   372 : mapping ( address => mapping( uint256 => StakedS1Citizen )) public stakedS1;

   378:  mapping ( address => uint256[] ) private _stakerS1Position;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L372-L378)

   405: mapping ( address => mapping( uint256 => StakedS2Citizen )) public stakedS2;

   411: mapping ( address => uint256[] ) private _stakerS2Position;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L405-L411)

   434: mapping ( address => LPPosition ) public stakerLPPosition;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L434)

##

### [G-5] CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS

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

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L717-L719)

     for (uint256 i; i < _stakerS2Position[_staker].length; ) {
     uint256 citizenId = _stakerS2Position[_staker][i];
     StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L734-L736)

  
     for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
     uint256 citizenId = _stakerS1Position[_recipient][i];
     StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1279-L1281)

    for (uint256 i; i < _stakerS2Position[_recipient].length; ) {
    uint256 citizenId = _stakerS2Position[_recipient][i];
    StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1288-L1290)

   for (uint256 i; i < windowCount; ) {
   RewardWindow memory window = pool.rewardWindows[i];

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1312-L1313)

   1320 : uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1320)

  1331: uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1331)

  1342: uint256 timeSinceReward = window.startTime - lastPoolRewardTime;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1342)

##

### [G-6] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

   910: if (citizenVaultId != 0 && vaultId != 0) {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L910)

   917: } else if (citizenVaultId != 0 && vaultId == 0) {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L917)

   926: } else if (citizenVaultId == 0 && vaultId != 0) {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L926)

##

### [G-7] INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT



FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

        37: bytes32 public constant BURN = keccak256("BURN");

        40: bytes32 public constant ADMIN = keccak256("ADMIN");

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L37-L40)

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

   206: bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

   209: bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);

   214: bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

   217: bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

   220: bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L206-L220)

##

### [G-8] <x> += <y> OR <x>-=<y> costs more gas than <x> = <x> + <y> OR  <x> = <x> - <y>   for state variables

Using the addition/Subtraction operator instead of plus-equals/minus-equals saves 113 gas

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

  977: pool.totalPoints += citizenStatus.points;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L977)

   1029 : pool.totalPoints += citizenStatus.points;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1029)

   1078: citizenStatus.stakedBytes += amount;
   1079: citizenStatus.points += bonusPoints;
   1080: pool.totalPoints += bonusPoints;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1078-L1080)

    1160: stakerLPPosition[msg.sender].amount += amount;
    1161: stakerLPPosition[msg.sender].points += points;

    1164: pool.totalPoints += points;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1160-L1164)

   1519 : pool.totalPoints -= stakedCitizen.points;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1515)

  1580 : pool.totalPoints -= stakedCitizen.points;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1580)

   1626: lpPosition.amount -= amount;
   1627: lpPosition.points -= points;
   1630: pool.totalPoints -= points;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1626-L1627)

  

##

### [G-9] Use constants instead of type(uintx).max

Using constants instead of type(uintx).max can save gas in some cases because constants are precomputed at compile-time and can be reused throughout the code, whereas type(uintx).max requires computation at runtime

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

  1017 : uint256 timelockMultiplier = _timelock & type(uint128).max;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1017)

  1141 : uint256 timelockMultiplier = _timelock & type(uint128).max;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1141)

##

### [G-10]  Use assembly to write address storage values


         BYTES = _bytes;
         S1_CITIZEN = _s1Citizen;
         S2_CITIZEN = _s2Citizen;
         LP = _lpToken;
         IDENTITY = _identity;
         VAULT = _vault;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L598)

##

### [G-11] ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS REQUIRE() OR IF-STATEMENT . This saves 200-210 gas as per remix

PROOF OF CONCEPT (remix without optimizations):

pragma solidity ^0.8.7;
contract MappingTest {

// Execution Cost 696
function Subtraction() public pure {
int a=20; int b=10; int c=10; int d=30;
    if(a>b){
        a=a-b;
    }
    if(c<d){
        c=d-c;
    }
   
}
//Execution Cost 276
function uncheckedSubtraction() public pure {
int a=20; int b=10; int c=10; int d=30;
    if(a>b){
        unchecked{a=a-b;}
        
    }
    if(c<d){
        unchecked{c=d-c;}
    }
   
}


}

So clearly for each unchecked possible to save 210 gas 

SOLUTION:

 require(a < b); x = b - a => require(a <b); unchecked { x = b - a } 

require(a > b); x = a - b => require(a > b); unchecked { x = a - b } 


FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

   1320 : uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1320)

   

  



   

    




  







GAS-1	Using bools for storage incurs overhead	1
GAS-2	Cache array length outside of loop	11
GAS-3	Don't initialize variables with default value	2
GAS-4	Pre-increments and pre-decrements are cheaper than post-increments and post-decrements	9
GAS-5	Using private rather than public for constants, saves gas	7
GAS-6	Use shift Right/Left instead of division/multiplication if possible	1
GAS-7	Use storage instead of memory for structs/arrays	15
GAS-8	Use != 0 instead of > 0 for unsigned integer comparison	4