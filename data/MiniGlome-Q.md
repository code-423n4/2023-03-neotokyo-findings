## [NC] Use built-in Solidity functions for string comparison instead of assembly code.
The function `_stringEquals` can be improved by using the `keccak256` hash function to compare the two strings. This method has a fixed gas cost and can handle arbitrary-length input strings, including Unicode characters. 

```solidity
function _stringEquals(string memory a, string memory b) private pure returns (bool) {
    return keccak256(abi.encodePacked(a)) == keccak256(abi.encodePacked(b));
}
```
This implementation is easier to read, less error-prone, and achieves the same result as the original implementation.

## [QA] Remove redundant code in `_stakedByes()`
`_stakedByes()` Can be refactored as such to remove redundant code:

```solidity
function _stakeBytes (
	uint256
) private {
	uint256 amount;
	uint256 citizenId;
	uint256 seasonId;
	assembly{
		amount := calldataload(0x44)
		citizenId := calldataload(0x64)
		seasonId := calldataload(0x84)
	}

	// Attempt to transfer BYTES to escrow.
	_assetTransferFrom(BYTES, msg.sender, address(this), amount);

	// Handle staking BYTES into an S1 Citizen.
	if (seasonId == 1) {
		StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
		PoolData storage pool = _pools[AssetType.S1_CITIZEN];
	// Handle staking BYTES into an S2 Citizen.
	} else if (seasonId == 2) {
		StakedS2Citizen storage citizenStatus = stakedS2[msg.sender][citizenId];
		PoolData storage pool = _pools[AssetType.S2_CITIZEN];
	// Revert because an invalid season ID has been supplied.
	} else {
		revert InvalidSeasonId(seasonId);
	}

	uint256 cap = VAULT_CAP;
	if (!citizenStatus.hasVault) {
		cap = NO_VAULT_CAP;
	}
	if (citizenStatus.stakedBytes + amount > cap) {
		revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
	}

	// Validate that the caller actually staked the Citizen.
	if (citizenStatus.timelockEndTime == 0) {
		revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
	}

	unchecked {
		uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
		citizenStatus.stakedBytes += amount;
		citizenStatus.points += bonusPoints;
		pool.totalPoints += bonusPoints;
	}

	// Emit an event.
	emit Stake(
		msg.sender,
		BYTES,
		(seasonId << 128) + citizenId,
		amount
	);
}
```