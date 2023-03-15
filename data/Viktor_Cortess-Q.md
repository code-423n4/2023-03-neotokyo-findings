### [NC] NAMED IMPORTS CAN BE USED
It’s possible to name the imports to improve code readability for all files in scope.

### [NC] MISSING EVENTS FOR FUNCTIONS THAT CHANGE CRITICAL PARAMETERS
The functions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

**contracts\staking\NeoTokyoStaker.sol**

    1760: function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) { 
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
		LP = _lp;
	}
    1905: function configureCaps (
		uint256 _vaultedCap,
		uint256 _unvaultedCap
	) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external { //@LOW Emit
		VAULT_CAP = _vaultedCap;
		NO_VAULT_CAP = _unvaultedCap;
	}

**contracts\staking\BYTES2.sol**

    163: function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}


### [NC] CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

**contracts\staking\NeoTokyoStaker.sol**

    1025: citizenStatus.points = 100 * timelockMultiplier / _DIVISOR
    1080: uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
    1101: uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
    1161: uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
    1429: uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
    1675: uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

### [NC] UNNECESSARY MATH OPERATIONS

**contracts\staking\NeoTokyoStaker.sol**

    200: uint256 constant private _DIVISOR = 100;

So this line: `1025: citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;`
Equal `citizenStatus.points = timelockMultiplier ;`

### [L] ADD ZERO ADDRESS VALIDATION IN CONSTRUCTOR 

The parameter used in the constructor is used to initialize the state variable, error in these can lead to the redeployment of the contract.

**contracts\staking\BYTES2.sol**

    75: constructor (
		address _bytes,
		address _s1Citizen,
		address _staker,
		address _treasury
	) {
		BYTES1 = _bytes;
		S1_CITIZEN = _s1Citizen;
		STAKER = _staker;
		TREASURY = _treasury;
	}

**contracts\staking\NeoTokyoStaker.sol**

    588: constructor (
		address _bytes,
		address _s1Citizen,
		address _s2Citizen,
		address _lpToken,
		address _identity,
		address _vault,
		uint256 _vaultCap,
		uint256 _noVaultCap
	) {
		BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
		LP = _lpToken;
		IDENTITY = _identity;
		VAULT = _vault;
		VAULT_CAP = _vaultCap;
		NO_VAULT_CAP = _noVaultCap;
	}

### [L] CHECKING ARRAY LENGTH BEFORE PROCESSING LOOP

If `_citizenRewardRates`, `_vaultRewardRates`, and `_identityCreditYields` arrays have different lengths, then Solidity will revert with an error. This is because the for loop is designed to iterate over the length of the `_citizenRewardRates` array, so if one of the other arrays is shorter or longer, then the loop will encounter an index out-of-bounds error when trying to access an element that doesn't exist.
To avoid this error, the function should first check that all three arrays have the same length before proceeding with the loop.

**contracts\staking\NeoTokyoStaker.sol**

    1812: function configureIdentityCreditYields (
		uint256[] memory _citizenRewardRates, 
		string[] memory _vaultRewardRates,
		string[] memory _identityCreditYields
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external { 
		for (uint256 i; i < _citizenRewardRates.length; ) {
			identityCreditYield[
				_citizenRewardRates[i]
			][
				_vaultRewardRates[i]
			] = _identityCreditYields[i];
			unchecked { ++i; }
		}
	}

Similar cases in 

    1837: function configureIdentityCreditPoints (
    1856: function configureVaultCreditMultipliers (

### [L] FUNCTION getReward CAN BE CALLED BY ANYBODY

**contracts\staking\BYTES2.sol**

In NatSpec we can see the following statement:

    /**
		This function is called by the S1 Citizen contract to emit BYTES to callers 
		based on their state from the staker contract.

		@param _to The reward address to mint BYTES to.
	*/

But there are no modifiers or require/if statements in the code:

    function getReward (
		address _to
	) external {
		
		(
			uint256 reward,
			uint256 daoCommision
		) = IStaker(STAKER).claimReward(_to); 
		
		// Mint both reward BYTES and the DAO tax to targeted recipients.
		if (reward > 0) {
			_mint(_to, reward);
		}
		if (daoCommision > 0) {
			_mint(TREASURY, daoCommision);
		}
	}


As a result, anybody can claim rewards for another person. 
This line from Tests will work:

    await NTBytes2_0.connect(nonCitizen.signer).getReward(bob.address)

### [L] USING THE 'lastRewardTime' MAPPING IN THE 'claimReward' FUNCTION MAY RESULT IN INCORRECT INFORMATION BEING STORED.

**contracts/staking/NeoTokyoStaker.sol**

          1433:lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;
		lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;
		lastRewardTime[_recipient][AssetType.LP] = block.timestamp;

Even if the user stakes only one kind of asset, for example, LP token, his data in mapping lastRewardTime will be rewritten for all 3 assets. 
Even if it doesn't affect sums of the rewards, this process could be redesigned to bring proper information to the users.

For example, during staking, you can add an internal function _setTime(msg.sender,_assetType) in the function stake before calling getReward:

            1224: _setTime(msg.sender,_assetType);
		// Grant the caller their total rewards with each staking action.
		IByteContract(BYTES).getReward(msg.sender); 

    //sets times of staking start for tokens that haven't been in the stake before:
    function _setTime(address staker,AssetType _assetType) internal {
		if (lastRewardTime[staker][_assetType] == 0){
			lastRewardTime[staker][_assetType] = block.timestamp;
		}
		
	}

And also add if statements for writing lastTimeRewards inside the function getPoolReward during rewards counting for each asset Type:

    if (_assetType == AssetType.LP) lastRewardTime[_recipient][AssetType.LP] = block.timestamp;

### [L] DIVISION BEFORE MULTIPLICATION CAN LEAD TO LOSSES IN PRECISION

it is recommended to perform multiplication before division to avoid loss of precision when working with integer types.

When dividing two integers, the result is rounded down to the nearest integer. This can lead to unexpected results when working with fractions or decimals, especially when performing multiple operations in succession.

**contracts\staking\NeoTokyoStaker.sol**

    1155: uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;