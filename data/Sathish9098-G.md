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

##

### [G-3] Setting the constructor to payable

ou can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

   75 : constructor (

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L75)




  







GAS-1	Using bools for storage incurs overhead	1
GAS-2	Cache array length outside of loop	11
GAS-3	Don't initialize variables with default value	2
GAS-4	Pre-increments and pre-decrements are cheaper than post-increments and post-decrements	9
GAS-5	Using private rather than public for constants, saves gas	7
GAS-6	Use shift Right/Left instead of division/multiplication if possible	1
GAS-7	Use storage instead of memory for structs/arrays	15
GAS-8	Use != 0 instead of > 0 for unsigned integer comparison	4