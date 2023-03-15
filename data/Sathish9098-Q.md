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

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

       598: BYTES = _bytes;
       599: S1_CITIZEN = _s1Citizen;
       600: S2_CITIZEN = _s2Citizen;
       601: LP = _lpToken;
       602: IDENTITY = _identity;
       603: VAULT = _vault;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L598-L603)

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

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

     604: VAULT_CAP = _vaultCap;
     605: NO_VAULT_CAP = _noVaultCap;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L604-L605)

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

     function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
		LP = _lp;
	}
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1708-L1715)

     

##

### [5] LOSS OF PRECISION DUE TO ROUNDING

Rounding is a process of approximating a number by replacing it with a nearby value that has fewer digits. In computing, rounding is often used to reduce the number of decimal places or significant digits in a calculation to a level that is appropriate for the context in which the result will be used. However, rounding can lead to a loss of precision in the calculation, which can be a problem in some situations

For example, suppose we want to calculate the average of two numbers, 1.23456789 and 9.87654321, to two decimal places. The exact answer is 5.55555555, but if we round each number to two decimal places before averaging, we get 1.23 and 9.88, which give an average of 5.555. This answer is less precise than the exact answer, and the error introduced by rounding can accumulate in more complex calculations

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

       968: citizenStatus.points = identityPoints * vaultMultiplier * timelockMultiplier /_DIVISOR / _DIVISOR;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L968-L970)

     1022: citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1022)

    1077: uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1077)

   1155: uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1155)

    1388: uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
    1389: uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1388-L1389)

   1623: uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1623)

##

### [6] Use OpenZeppelin safeCast library instead of normal casting

The SafeCast library provides a set of functions for safely casting between integer types in Solidity. These functions perform various checks to prevent integer overflows and underflows that can occur when performing type conversions

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

  1205: if (uint8(_assetType) > 4) { //@audit uint8 safecast

  1206: revert InvalidAssetType(uint256(_assetType));  //@audit uint256 safecast

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205-L1206)

   1211: revert UnconfiguredPool(uint256(_assetType));

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1211)

  1216: revert InactivePool(uint256(_assetType));

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1216)

  1222: revert InvalidTimelockOption(uint256(_assetType), _timelockId);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1222)

  1307: revert InvalidAssetType(uint256(_assetType));

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1301)

    1668: if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
    1669: revert InvalidAssetType(uint256(_assetType));

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668-L1669)

Recommended Mitigation:

Use OpenZeppelin safeCast library 


##

### [7] Gas griefing/theft is possible on unsafe external call

return data (bool success,) has to be stored due to EVM architecture, if in a usage like below, ‘out’ and ‘outsize’ values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided

(https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19)

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

          (bool success, bytes memory data) = 
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_FROM_SELECTOR,
					_from,
					_to, 
					_idOrAmount
				)
			);

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L772-L780)

##

### [L-8] Add an event for critical parameter changes

Adding events for critical parameter changes will facilitate offchain monitoring and indexing

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

     function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
		LP = _lp;
	}
(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1708-L1715)

Recommended Mitigation Steps :

Add events for every critical changes happen in protocol with old and new values 

##

### [L-9] Tokens accidentally sent to the contract cannot be recovered

It can’t be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

Recommended Mitigation Steps

Add this code:

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

##

### [L-10] Use safest version of openzeppelin/contracts-upgradeable instead of vulnerable one

"@openzeppelin/contracts-upgradeable": "^4.4.2" version used in this protocol

Recommended mitigation:

Use safest openzeppelin version 4.8.2

  
# NONCRITICAL FINDINGS 

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

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

    import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
    import "../access/PermitControl.sol";
    import "../interfaces/IByteContract.sol";
    import "../interfaces/IGenericGetter.sol";

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L4-L8)

##

### [2] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

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

### [3] Use "public immutable " constantly instead of "immutable public"

in Solidity, the order of modifiers in a variable declaration matters. The public modifier should come before the immutable modifier.

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

   43: address immutable public BYTES1;

   46: address immutable public S1_CITIZEN;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L43-L46)

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

    223:  address immutable public BYTES;
 
    226:  address immutable public S1_CITIZEN;

    229:  address immutable public S2_CITIZEN;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L223-L229)

   239: address immutable public IDENTITY;

   242: address immutable public VAULT;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L239-L242)

   
##

### [4] FOR STATE VARIBAES, FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS

it is common practice to use lowercase letters for variable names, and to use capital letters for contract names and event names
Using capital letters for state variable names may cause confusion or make the code harder to read, especially if the convention is not consistent throughout the codebase. Therefore, it is generally recommended to use lowercase letters for state variable names.

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

   49: address public STAKER;

   52: address public TREASURY;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L49-L52)
FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

   232: address public LP;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L232)

##

### [5] Use @notice tag to explain the functions as per NATSPEC instructions

CONTEXT: 
ALL FUNCTIONS

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

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol 

   728 : unchecked { i++; }

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L728) 

  743: unchecked { i++; }

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L743)

  unchecked {
   citizenStatus.points =
				identityPoints * vaultMultiplier * timelockMultiplier /
				_DIVISOR / _DIVISOR;
			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

			// Record the caller's staked S1 Citizen.
			_stakerS1Position[msg.sender].push(citizenId);

			// Update the pool point weights for rewards
			pool.totalPoints += citizenStatus.points;
		}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L967-L978)

      unchecked {
			citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

			// Record the caller's staked S2 Citizen.
			_stakerS2Position[msg.sender].push(citizenId);

			// Update the pool point weights for rewards
			pool.totalPoints += citizenStatus.points;
		}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1021-L1030)

          unchecked {
				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
				citizenStatus.stakedBytes += amount;
				citizenStatus.points += bonusPoints;
				pool.totalPoints += bonusPoints;
			}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1076-L1081)

     unchecked {
     points += s1Citizen.points;
     i++;
     }

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1282-L1285)

      1291 : unchecked {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1291)

     1297: unchecked {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1297)

    1330 : unchecked {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1330)

   1341: unchecked {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1341)

  1354: unchecked {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1354)

  1369 : unchecked { j++; }

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1369)

   1377: unchecked {

   1383 : unchecked { i++; }

   1387: unchecked {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1387)

   1509 : unchecked { stakedIndex++; }

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1509)
   
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

     function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
		LP = _lp;
	}

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1708-L1715) 

Recommended Mitigation:

 require(oldAddress!= _newAddress, "Same Address");

##

### [10] CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

 Even assembly can benefit from using readable constants instead of hex/numeric literals

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

   203 : uint256 constant private _BYTES_PER_POINT = 200 * 1e18;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L203)


##

### [11] Use "private constant" instead of "constant private"

in Solidity, the order of modifiers in a variable declaration matters. The private modifier should come before the immutable modifier.

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

    191 : bytes4 constant private _TRANSFER_FROM_SELECTOR = 0x23b872dd;

    194:  bytes4 constant private _TRANSFER_SELECTOR = 0xa9059cbb;

    197:  uint256 constant private _PRECISION = 1e12;

    200:  uint256 constant private _DIVISOR = 100;

    203:  uint256 constant private _BYTES_PER_POINT = 200 * 1e18;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L191-L203)

##

### [12] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

It is generally considered best practice to specify a fixed compiler version in non-library/interface files in Solidity, rather than using a floating version. This is because different versions of the Solidity compiler can introduce changes to the language syntax or behavior, which may cause compatibility issues or unexpected behavior in your code

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

    2: pragma solidity ^0.8.19;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L2)

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

   2: pragma solidity ^0.8.19;

##

### [13] GENERATE PERFECT CODE HEADERS EVERY TIME

Description
I recommend using header for Solidity code layout and readability

(https://github.com/transmissions11/headers)

##

### [14] Contract layout and order of functions

Another [recommendation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) is to declare internal functions below external functions.

Functions should be grouped according to their visibility and ordered:

constructor

receive function (if exists)

fallback function (if exists)

external

public

internal

private

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

The instances below highlights private above external. If possible, consider adding private functions below external functions for the contract layout

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1597-L1662)

##

### [15] Use constants instead of hardcoding numbers

It is generally recommended to use constants instead of hardcoding numbers directly in your code. This can help make your code more readable and maintainable, as well as avoid errors that may arise from accidentally mistyping a number

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

  962: uint256 timelockDuration = _timelock >> 128;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L962)

   1016: uint256 timelockDuration = _timelock >> 128;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1016)

  1022: citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1022)

  1113: (seasonId << 128) + citizenId,

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1113)

   1140: uint256 timelockDuration = _timelock >> 128;

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1140)

   1205: if (uint8(_assetType) > 4) {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205)

   1668: if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668-L1669)


##

### [16] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses


FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

       bytes4 constant private _TRANSFER_FROM_SELECTOR = 0x23b872dd;
       bytes4 constant private _TRANSFER_SELECTOR = 0xa9059cbb;
       uint256 constant private _PRECISION = 1e12;
       uint256 constant private _DIVISOR = 100;
       uint256 constant private _BYTES_PER_POINT = 200 * 1e18;
       bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
       bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);
       bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
       bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
       bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L191-L220)

FILE : 2023-03-neotokyo/contracts/staking/BYTES2.sol

        bytes32 public constant BURN = keccak256("BURN");
        bytes32 public constant ADMIN = keccak256("ADMIN");

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L37-L40)

##

### [17] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

FILE : 2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol

   833: assembly {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L833)

   886: assembly {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L886)

   1001: assembly {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1001)
  
   1050: assembly{

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1050)

   1128: assembly{

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1128)

  1236: assembly {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1236)

   1461: assembly {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1461)

   1536: assembly {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1536)

  1599: assembly{

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1599)

  1682: assembly {

(https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1682)

##

### [18] Convert repeated validation statements into a function modifier to improve code reusability

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




  





 


 
  

    



  



























