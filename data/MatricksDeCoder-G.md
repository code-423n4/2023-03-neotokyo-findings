GAS-1 Make constructor payable to cut on deployment gas fees as constructors with payable cut out some opcodes used to check if value has been sent with constructor in order to revert 
[BYTES2.sol Lines 75-83]
[NeoTokyoStaker.sol lines 588 - 606] 


GAS-2 Reuse existing memory variable from parameter _amount and remove uint256 treasuryShare, so as to not create unnecessary additional memory variable. [BYTES2.sol lines 150 - 154] 
               unchecked {
			_amount = _amount * 2 / 3;
		}
		_mint(TREASURY, _amount);


GAS-3 Simple setter function for state storage variable can benefit from using assembly without affecting readability but saving on gas fees to set and change the variable
[BYTES2.sol lines 162 - 166] 
function changeStakingContractAddress (address _staker) hasValidPermit(UNIVERSAL, ADMIN) external {
       assembly {
             sstore(STAKER.slot, _staker)
       }
}
[BYTES2.sol lines 173 - 177] 
function changeTreasuryContractAddress (address _treasury) hasValidPermit(UNIVERSAL, ADMIN) external {
       assembly {
             sstore(TREASURY.slot, _treasury)
       }
}
[NeoTokyoStaker.sol lines 1851 - 1858] 
function configureCaps (uint256 _vaultedCap,uint256 _unvaultedCap) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external {
      assembly {
          sstore(VAULT_CAP.slot, _vaultedCap)
          sstore(NO_VAULT_CAP.slot, _unvaultedCap)
      } 
}
[NeoTokyoStaker.sol lines 1708 - 1715]
	function configureLP (address _lp) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
                assembly {
                       sstore(LP.slot, _lp)
                }
	}
[NeoTokyoStaker.sol lines 1721-1724]
	function lockLP () external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		assembly {
                   sstore(lpLocked.slot, true)
                }
	}


GAS-4 Cache storage variables used throughout a function in memory to save on gas costs of having to reread the values from storage if the values are used more than once in a function. 
[NeoTokyoStaker.sol line from 1459 _withdrawS1Citizen ()] stakedCitizen.timelockEndTime is read in lines 1467,1472 can be cached into e.g uint256 _timelockEndTime = stakedCitizen.timelockEndTime
[NeoTokyoStaker.sol line from 1459 _withdrawS1Citizen ()] stakedCitizen.stakedBytes is read in lines s1477,1478 can be cached into e.g uint256 _stakedBytes = stakedCitizen.stakedBytes
[NeoTokyoStaker.sol line from 1459 _withdrawS1Citizen ()] stakedCitizen.stakedVaultId is read in lines 1482,1487 can be cached into e.g uint256 _stakedVaultId = stakedCitizen.stakedVaultId
[NeoTokyoStaker.sol line from 1534 _withdrawS2Citizen ()] stakedCitizen.timelockEndTime is read in lines 1542,1543,1547 can be cached into e.g uint256 _timelockEndTime = stakedCitizen.timelockEndTime
[NeoTokyoStaker.sol line from 1534 _withdrawS2Citizen ()] stakedCitizen.stakedBytes is read in lines 1552,1553 can be cached into e.g uint256 _stakedBytes = stakedCitizen.stakedBytes
[NeoTokyoStaker.sol line from 1597 _withdrawLP ()] lpPosition.timelockEndTime is read in lines 1605,1606 can be cached into e.g uint256 _timelockEndTime = lpPosition.timelockEndTime
[NeoTokyoStaker.sol line from 1597 _withdrawLP ()] lpPosition.amount is read in lines 1610,1611 can be cached into e.g uint256 _amount = lpPosition.timelockEndTime


GAS-5 Make use of calldata instead of memory for external function parameters such as arrays when the arguments are read only. Using calldata avoids the abi.decode() loop which to to copy each index of the calldata to the memoryindex which will cost more gas. 
[NeoTokyoStaker.sol line 1739] uint256[] memory _timelockIds
[NeoTokyoStaker.sol line 1740] uint256[] memory _encodedSettings
[NeoTokyoStaker.sol line 1761] uint256[] memory _citizenRewardRates
[NeoTokyoStaker.sol line 1762] string[] memory _vaultRewardRates
[NeoTokyoStaker.sol line 1763] string[] memory _identityCreditYields
[NeoTokyoStaker.sol line 1784] string[] memory _identityCreditYields,
[NeoTokyoStaker.sol line 1785] uint256[] memory _points
[NeoTokyoStaker.sol line 1803] string[] memory _vaultCreditMultipliers,
[NeoTokyoStaker.sol line 1804] uint256[] memory _multipliers
[NeoTokyoStaker.sol line 1820] PoolConfigurationInput[] memory _inputs