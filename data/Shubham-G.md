## [G-01] Use assembly to write address storage values

Contract: BYTES2.sol
Before:

     L: 75       constructor (
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


STAKER & TREASURY can be written using assembly since the other two (BYTES1 & S1_CITIZEN) are immutable, assembly  cannot be used. Assigning these two saves up to 460 gas.
After:

    L:75	constructor(
        	   address _bytes,
       	   address _s1Citizen,
        	   address _staker,
     	   address _treasury
         ) {
       	 BYTES1 = _bytes;
       	S1_CITIZEN = _s1Citizen;

        	assembly {
            	sstore(STAKER.slot, _staker)
            	sstore(TREASURY.slot, _treasury)
        	}
    }


Contract: NeoTokyoStaker.sol
Before:

    L: 588 	constructor (
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

LP, VAULT_CAP & NO_VAULT_CAP can be written using assembly since the other two are immutable can assembly cannot be used. Assigning these two saves up to 311 gas.
After:

    L: 588 	constructor(
                address _bytes,
                address _s1Citizen,
                address _s2Citizen,
                address _lpToken,
                address _identity,
                address _vault,
                uint256 _vaultCap,
                uint256 _noVaultCap
    ) payable {
        BYTES = _bytes;
        S1_CITIZEN = _s1Citizen;
        S2_CITIZEN = _s2Citizen;
        IDENTITY = _identity;
        VAULT = _vault;

        assembly {
            sstore(LP.slot, _lpToken)
            sstore(VAULT_CAP.slot, _vaultCap)
            sstore(NO_VAULT_CAP.slot, _noVaultCap)
        }
    }


## [G-02] Setting the constructor to payable

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks.

Contract: BYTES2.sol
Before:

    L: 75 	constructor (address _bytes, address _s1Citizen, address _staker, address _treasury){

Here, adding `payable` saves approx. 212 gas

After:

    L:75	constructor(
                 address _bytes,
                 address _s1Citizen,
                 address _staker,
                 address _treasury
                 ) payable {


Contract: NeoTokyoStaker.sol
Before:

    L: 588	constructor (
		address _bytes, address _s1Citizen, address _s2Citizen,
		address _lpToken, address _identity, address _vault,
		uint256 _vaultCap, uint256 _noVaultCap
	) {

Here, adding `payable` saves approx. 224 gas

After:

    L:588	constructor(
                 address _bytes, address _s1Citizen, address _s2Citizen, 
                 address _lpToken, address _identity, address _vault, 
                 uint256 _vaultCap, uint256 _noVaultCap 
                 ) payable {

## [G-03] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

Contract: NeoTokyoStaker.sol
Before:

    L: 910		if (citizenVaultId != 0 && vaultId != 0) {

    L: 917		} else if (citizenVaultId != 0 && vaultId == 0) {

    L: 926		} else if (citizenVaultId == 0 && vaultId != 0) {

    L: 1834		if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {

Recommendation: Using this method saves up to 5661 gas.

    L:910		if (vaultId != 0) {
            	        if (citizenVaultId != 0) {
                		revert CitizenAlreadyHasVault(citizenVaultId, vaultId);
                        } }


    L:917		if (citizenVaultId != 0) {
            		if (vaultId == 0) {
               		 citizenStatus.hasVault = true;
                		 vaultId = citizenVaultId;
                        } }

    L: 926 		 if (citizenVaultId == 0) {
            		 if (vaultId != 0) {
                		     _assetTransferFrom(VAULT, msg.sender, address(this), vaultId);
               		      citizenStatus.hasVault = true;
                		      citizenStatus.stakedVaultId = vaultId;
           		       }
      		 }
		
    L: 1834		if (j != 0) {
                        if (_inputs[i].rewardWindows[j].startTime <= lastTime) {
                        revert RewardWindowTimesMustIncrease();
                    }
                }


## [G-04] Cache storage values in memory to minimize SLOADs

The code can be optimized by minimizing the number of SLOADs.
SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

Contract: NeoTokyoStaker.sol
Before:

    L: 1465       // Validate that the caller has cleared their asset timelock.
		      StakedS1Citizen storage stakedCitizen = stakedS1[msg.sender][citizenId];
		      if (block.timestamp < stakedCitizen.timelockEndTime) {
			   revert TimelockNotCleared(stakedCitizen.timelockEndTime);
		   }

		// Validate that the caller actually staked this asset.
		if (stakedCitizen.timelockEndTime == 0) {
			revert CannotWithdrawUnownedS1(citizenId);
		}
		
		// Return any staked BYTES.
		if (stakedCitizen.stakedBytes > 0) {
			_assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
		}
		
		// Return any non-component Vault if one is present.
		if (stakedCitizen.stakedVaultId != 0) {
			_assetTransferFrom(
				VAULT,
				address(this),
				msg.sender,
				stakedCitizen.stakedVaultId
			);
		}

		// Return the S1 Citizen.
		_assetTransferFrom(S1_CITIZEN, address(this), msg.sender, citizenId);

		/*
			Check each citizen ID to find its index and remove the token from the
			staked item array of its old position.
		*/
		uint256[] storage oldPosition = _stakerS1Position[msg.sender];
		for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {

			// Remove the element at the matching index.
			if (citizenId == oldPosition[stakedIndex]) {
				if (stakedIndex != oldPosition.length - 1) {
				oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
				}
				oldPosition.pop();
				break;
			}
			unchecked { stakedIndex++; }
		}

		// Update caller staking information and asset data.
		PoolData storage pool = _pools[AssetType.S1_CITIZEN];
		unchecked {
			pool.totalPoints -= stakedCitizen.points;
		}
		stakedCitizen.stakedBytes = 0;
		stakedCitizen.timelockEndTime = 0;
		stakedCitizen.points = 0;
		stakedCitizen.hasVault = false;
		stakedCitizen.stakedVaultId = 0;

After: Assign ` stakedCitizen.timelockEndTime` & ` stakedCitizen.stakedVaultId ` to memory. Saves up to 3720 gas.

    L: 1465    // Validate that the caller has cleared their asset timelock.
               StakedS1Citizen storage stakedCitizen = stakedS1[msg.sender][citizenId];

               uint256 _stakedCitizenTimelockEndTime = stakedCitizen.timelockEndTime;
               uint256 _stakedCitizenStakedVaultId = stakedCitizen.stakedVaultId;

               if (block.timestamp < _stakedCitizenTimelockEndTime) {
                   revert TimelockNotCleared(_stakedCitizenTimelockEndTime);
               }

        // Validate that the caller actually staked this asset.
        if (_stakedCitizenTimelockEndTime == 0) {
            revert CannotWithdrawUnownedS1(citizenId);
        }

        // Return any staked BYTES.
        if (stakedCitizen.stakedBytes > 0) {
            _assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
        }

        // Return any non-component Vault if one is present.
        if (_stakedCitizenStakedVaultId != 0) {
            _assetTransferFrom(
                VAULT,
                address(this),
                msg.sender,
                _stakedCitizenStakedVaultId
            );
        }

        // Return the S1 Citizen.
        _assetTransferFrom(S1_CITIZEN, address(this), msg.sender, citizenId);

        /*
            Check each citizen ID to find its index and remove the token from the
            staked item array of its old position.
        */
        uint256[] storage oldPosition = _stakerS1Position[msg.sender];
        for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
            // Remove the element at the matching index.
            if (citizenId == oldPosition[stakedIndex]) {
                if (stakedIndex != oldPosition.length - 1) {
                    oldPosition[stakedIndex] = oldPosition[
                        oldPosition.length - 1
                    ];
                }
                oldPosition.pop();
                break;
            }
            unchecked {
                stakedIndex++;
            }
        }

        // Update caller staking information and asset data.
        PoolData storage pool = _pools[AssetType.S1_CITIZEN];
        unchecked {
            pool.totalPoints -= stakedCitizen.points;
        }
        stakedCitizen.stakedBytes = 0;
       stakedCitizen.points = 0;
       _stakedCitizenTimelockEndTime = 0;
       _stakedCitizenStakedVaultId = 0;
        stakedCitizen.hasVault = false;


Contract: NeoTokyoStaker.sol
Before:

    L: 1540     // Validate that the caller has cleared their asset timelock.
		    StakedS2Citizen storage stakedCitizen = stakedS2[msg.sender][citizenId];
		     if (block.timestamp < stakedCitizen.timelockEndTime) {
			   revert TimelockNotCleared(stakedCitizen.timelockEndTime);
		    }

		// Validate that the caller actually staked this asset.
		if (stakedCitizen.timelockEndTime == 0) {
			revert CannotWithdrawUnownedS2(citizenId);
		}

		// Return any staked BYTES.
		if (stakedCitizen.stakedBytes > 0) {
			_assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
		}

		// Return the S2 Citizen.
		_assetTransferFrom(S2_CITIZEN, address(this), msg.sender, citizenId);

		/*
			Check each citizen ID to find its index and remove the token from the
			staked item array of its old position.
		*/
		uint256[] storage oldPosition = _stakerS2Position[msg.sender];
		for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {

			// Remove the element at the matching index.
			if (citizenId == oldPosition[stakedIndex]) {
				if (stakedIndex != oldPosition.length - 1) {
				oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
				}
				oldPosition.pop();
				break;
			}
			unchecked { stakedIndex++; }
		}

		// Update caller staking information and asset data.
		PoolData storage pool = _pools[AssetType.S2_CITIZEN];
		unchecked {
			pool.totalPoints -= stakedCitizen.points;
		}
		stakedCitizen.stakedBytes = 0;
		stakedCitizen.timelockEndTime = 0;
		stakedCitizen.points = 0;

After: Assign ` stakedCitizen.timelockEndTime` & ` stakedCitizen.stakedByte` to memory. Saves up to 4712 gas.

    L: 1540    // Validate that the caller has cleared their asset timelock.
               StakedS2Citizen storage stakedCitizen = stakedS2[msg.sender][citizenId];

               uint256 _stakedCitizenTimelockEndTime = stakedCitizen.timelockEndTime;
               uint256 _stakedCitizenStakedBytes = stakedCitizen.stakedBytes;

        if (block.timestamp < _stakedCitizenTimelockEndTime) {
            revert TimelockNotCleared(_stakedCitizenTimelockEndTime);
        }

        // Validate that the caller actually staked this asset.
        if (_stakedCitizenTimelockEndTime == 0) {
            revert CannotWithdrawUnownedS2(citizenId);
        }

        // Return any staked BYTES.
        if (_stakedCitizenStakedBytes > 0) {
            _assetTransfer(BYTES, msg.sender, _stakedCitizenStakedBytes);
        }

        // Return the S2 Citizen.
        _assetTransferFrom(S2_CITIZEN, address(this), msg.sender, citizenId);

        /*
            Check each citizen ID to find its index and remove the token from the
            staked item array of its old position.
        */
        uint256[] storage oldPosition = _stakerS2Position[msg.sender];
        for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
            // Remove the element at the matching index.
            if (citizenId == oldPosition[stakedIndex]) {
                if (stakedIndex != oldPosition.length - 1) {
                    oldPosition[stakedIndex] = oldPosition[
                        oldPosition.length - 1
                    ];
                }
                oldPosition.pop();
                break;
            }
            unchecked {
                stakedIndex++;
            }
        }

        // Update caller staking information and asset data.
        PoolData storage pool = _pools[AssetType.S2_CITIZEN];
        unchecked {
            pool.totalPoints -= stakedCitizen.points;
        }
        _stakedCitizenStakedBytes = 0;
        _stakedCitizenTimelockEndTime = 0;
        stakedCitizen .points = 0;

## [G-05] Gas Saving

Instead of initializing the components individually, the recommended way (in this situation) reduces the consumption by 204 gas.

Contract: NeoTokyoStaker.sol
Before:

    L: 1517	    stakedCitizen.stakedBytes = 0;
		    stakedCitizen.timelockEndTime = 0;
		    stakedCitizen.points = 0;
		    stakedCitizen.hasVault = false;
		    stakedCitizen.stakedVaultId = 0;

After:

    L:1517    stakedCitizen.stakedBytes = stakedCitizen.points =
              _stakedCitizenTimelockEndTime = _stakedCitizenStakedVaultId = 0;
              stakedCitizen.hasVault = false;



