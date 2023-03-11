# LOW FINDINGS

## [1] MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES

It is important to always check for the value address(0x0) (also known as address(0)) when assigning values to address state variables. This is because address(0x0) represents the Ethereum zero address, which has a special meaning and can cause unexpected behavior if not properly handled

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

      81: BYTES1 = _bytes;
      82: S1_CITIZEN = _s1Citizen;
      83: STAKER = _staker;
      84: TREASURY = _treasury;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L81-L84)

       function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L162-L166)

       function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L173-L177)  

Recommended Mitigation:
 
Add address(0) checks before assigning value to state variables 

##

### [2] Using the safeMint() instead of the normal mint()

The safeMint() function is typically used in contracts that implement the ERC20 or ERC721 standards, which are widely adopted standards for creating fungible and non-fungible tokens on the Ethereum blockchain. The safeMint() function is designed to prevent potential security vulnerabilities in these token contracts by including additional checks and safeguards to prevent certain types of attacks, such as integer overflows or reentrancy attacks

OpenZeppelin [recommendation](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol#L277) is to use the safe variant of _mint.

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

  102 : _mint(msg.sender, _amount);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L102)

   124: _mint(_to, reward);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L124)

   127 : _mint(TREASURY, daoCommision);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L127)

   154: _mint(TREASURY, treasuryShare);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L154)

Recommended Mitigation:

Replace _mint() with _safeMint()

##

### [3] LACK OF CHECKS THE INTEGER RANGES

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

_amountis not checked to be != 0 before calling the _burn()

    function burn (address _from,uint256 _amount) hasValidPermit(UNIVERSAL, BURN) external {
   _burn(_from, _amount);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L140-L144)

##

### [4] CRITICAL ADDRESS CHANGES SHOULD USE TWO-STEP PROCEDURE  

The critical procedures should be two step process.

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

       function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L162-L166)

       function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L173-L177)







# NONCRITICAL FINDINGS ()

##

### [1] Named imports can be used

It’s possible to name the imports to improve code readability. E.g. import "@openzeppelin/contracts/token/ERC20/IERC20.sol"; can be rewritten as import {IERC20} from “import “@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol”;

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol
  
     import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
     import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

     import "../access/PermitControl.sol";
     import "../interfaces/IByteContract.sol";
     import "../interfaces/IStaker.sol";

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L4-L9)

##

### [2] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

        37: bytes32 public constant BURN = keccak256("BURN");

        40: bytes32 public constant ADMIN = keccak256("ADMIN");

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L37-L40)

##

### [3] Use "public immutable" instead of "immutable public"

in Solidity, the order of modifiers in a variable declaration matters. The public modifier should come before the immutable modifier.

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

   43: address immutable public BYTES1;

   46: address immutable public S1_CITIZEN;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L43-L46)

##

### [4] FOR STATE VARIBAES, FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS

it is common practice to use lowercase letters for variable names, and to use capital letters for contract names and event names
Using capital letters for state variable names may cause confusion or make the code harder to read, especially if the convention is not consistent throughout the codebase. Therefore, it is generally recommended to use lowercase letters for state variable names.

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

   49: address public STAKER;

   52: address public TREASURY;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L49-L52)

##

### [5] Use @notice tag to explain the functions as per NATSPEC instructions

When this function is compiled, the Natspec comments containing the @notice and @param tags are included in the contract's metadata

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

         /**
                Allow holders of the old BYTES contract to change them for BYTES 2.0; old 
		BYTES tokens will be burnt.
		@param _amount The amount of old BYTES tokens to exchange.
	*/
	function upgradeBytes (

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L87-L93)

        /**
		This function is called by the S1 Citizen contract to emit BYTES to callers 
		based on their state from the staker contract.
		@param _to The reward address to mint BYTES to.
	*/
	function getReward (
		address _to
	) external {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L108-L116)

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L108-L114)

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L131-L140)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L157-L162)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L169-L173)
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L179-L189)

##

### [6] Lack of address(0) checks before calling the functions.

If the _to is zero address the overall functions executions waste of work

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

        function getReward (
		address _to
	) external {
        (uint256 reward,uint256 daoCommision) = IStaker(STAKER).claimReward(_to);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L114-L120)

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L140-L144)

##

### [7] Need Fuzzing test

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

     151: unchecked {
          treasuryShare = _amount * 2 / 3;
          }

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L151-L153)

##

### [8] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

       function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L162-L166)

       function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L173-L177)  

##

### [9]  NO SAME VALUE INPUT CONTROL

Add an additional check to ensure that the input is not the same as the previously used value, thus preventing same value inputs in a more robust way

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

       function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L162-L166)

       function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L173-L177)  

Recommended Mitigation:

 require(TREASURY  != _treasury, "Same Address");





  



























NC-1	Typos	7
L-1	Empty Function Body - Consider commenting why	2