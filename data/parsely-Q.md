# [N-01] Incorrect description in NatSpec comment of parameter
## Context 
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L70

```
		@param _bytes The address of the BYTES 2.0 ERC-20 token contract.
		@param _s1Citizen The address of the assembled Neo Tokyo S1 Citizen.
		@param _staker The address of the new BYTES emitting staker.
		@param _treasury The address of the DAO treasury.
	*/
	constructor (
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
```
## Description
The parameter specifies the ```_bytes``` parameter is of type ```BYTES 2.0 ERC-20``` however, according to the code it is intended to be of type BYTES 1.0 as shown in the line ```BYTES1 = _bytes;``` and the address is used to in the ```upgradeBytes``` function to burn the old BYTES 1.0 tokens.

## Resolution
Correct the description in the NatSpec comment

# [N-02] Input parameters are not checked in constructor
## Context 
Constructor function:BYTES2.sol
```
		@param _bytes The address of the BYTES 2.0 ERC-20 token contract.
		@param _s1Citizen The address of the assembled Neo Tokyo S1 Citizen.
		@param _staker The address of the new BYTES emitting staker.
		@param _treasury The address of the DAO treasury.
	*/
	constructor (
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
```
## Description
The input values into the constructor are never checked in any way and the rest of the code in the contract assumes the values to be correct.

## Resolution
Check the input addresses for address(0) and perhaps that they are indeed contracts.

# [N-03] Even if the AssetType is invalid the code to check for InvalidAsseType will never be reached.
## Context 
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1264-L1302

```
	function getPoolReward (
		AssetType _assetType,
		address _recipient
	) public view returns (uint256, uint256) {

		/*
			During the very first stake, there will not be any points in the pool. In 
			this case, do not attempt to grant any rewards so as to prevent reversion.
		*/
		PoolData storage pool = _pools[_assetType];
		if (pool.totalPoints != 0) {

			// Calculate the total number of points accrued to the `_recipient`.
			uint256 points;
			if (_assetType == AssetType.S1_CITIZEN) {
				for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
					uint256 citizenId = _stakerS1Position[_recipient][i];
					StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
					unchecked {
						points += s1Citizen.points;
						i++;
					}
				}
			} else if (_assetType == AssetType.S2_CITIZEN) {
				for (uint256 i; i < _stakerS2Position[_recipient].length; ) {
					uint256 citizenId = _stakerS2Position[_recipient][i];
					StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];
					unchecked {
						points += s2Citizen.points;
						i++;
					}
				}
			} else if (_assetType == AssetType.LP) {
				unchecked {
					points += stakerLPPosition[_recipient].points;
				}
			} else {
				revert InvalidAssetType(uint256(_assetType));
			}
```
## Description
If an invalid asset value is sent in, it will not enter the if statement ```if (pool.totalPoints != 0) {```, and the ```revert InvalidAssetType(uint256(_assetType));``` will never be reached.
## Resolution
Either remove the unreachable code or implement the check higher up in the method.
# [N-04] Incorrect check for AssetType values in if statements
## Context 
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1196-L1207

```
		if (uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
```
also
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1659-L1670

```
		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
```

## Description
The enum for AssetType only has 4 items in it, and thus an index of 4 would not be valid in the enum
```
enum AssetType {
		S1_CITIZEN,
		S2_CITIZEN,
		BYTES,
		LP
	}
```
The checks above all check if the input parameter is greater than 4 instead of greater than 3. This bypasses the check for ```InvalidAssetType```. Although the code does revert and the impact is low it is good practice to catch errors before they are reported by the EVM, and revert with a custom error.

## Resolution
Correct the value in if statements.

# [N-05] Input parameter of type array,the lengths are not checked to be equal
## Context 
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1737-L1746
```
	function configureTimelockOptions (
		AssetType _assetType,
		uint256[] memory _timelockIds,
		uint256[] memory _encodedSettings
	) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
		for (uint256 i; i < _timelockIds.length; ) {
			timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];
			unchecked { ++i; }
		}
	}
```

also 
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1760-L1773
```
	function configureIdentityCreditYields (
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
```
also
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1783-L1791
```
	function configureIdentityCreditPoints (
		string[] memory _identityCreditYields,
		uint256[] memory _points
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
		for (uint256 i; i < _identityCreditYields.length; ) {
			identityCreditPoints[_identityCreditYields[i]] = _points[i];
			unchecked { ++i; }
		}
	}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1802-L1810
```
	function configureVaultCreditMultipliers (
		string[] memory _vaultCreditMultipliers,
		uint256[] memory _multipliers
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
		for (uint256 i; i < _vaultCreditMultipliers.length; ) {
			vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];
			unchecked { ++i; }
		}
	}
```
## Description
In the above functions the array values correspond to each other via the index in the array. The input array's length's are not checked to be equal, this will cause a revert so the impact is not high, however it is good practice to catch errors before they are reported by the EVM, and revert with a custom error.

## Resolution
Implement checks to require array length's to be equal.

# [N-06] Input parameters are not checked in configuring LPtoken contract address. does not check for zero address.
## Context 
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1708-L1715
```
	function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
		LP = _lp;
	}
```
## Description
The value of _lp input parameter is not checked if it is a valid contract or if it is set to address(0).

## Resolution
Implement checks for the _lp input parameter like codesize checks and not equal to address(0).

# [N-07] Functions called by privileged users and make significant changes to the contract should emit events.
## Context 
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173
```function changeTreasuryContractAddress (```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162
```function changeStakingContractAddress (```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1851
```function configureCaps (```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1819
```function configurePools (```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1802
```function configureVaultCreditMultipliers (```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1783
```function configureIdentityCreditPoints (```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1760
```function configureIdentityCreditYields (```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1737
``function configureTimelockOptions (```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1721
```function lockLP () external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1708
```function configureLP (```
## Description
Functions called by privileged users and make significant changes to the contract should emit events.

## Resolution
Implement functions to emit events.