[G1] With assembly, .call (bool success) transfer can be done gas-optimized

“(bool success, bytes memory returnData) = target.call()" automatically copies the return data to memory even if you omit the returnData variable, if a relayer executes transactions with such calls it can lead to a gas-griefing attack

The proper way to handle this is to do a low-level Yul "call" instead, with "out" and "outsize" argument values are zero. It looks like "success := call(gas, target, value, add(calldata, 0x20), mload(calldata), 0, 0)" where the last 2 args are "out" & "outsize" and are both 0

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

function _assetTransfer (
		address _asset,
		address _to,
		uint256 _amount
	) private {
		(bool success, bytes memory data) = 
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_SELECTOR,
					_to, 
					_amount
				)
			);

		// Revert if the low-level call fails.
		if (!success) {
			revert(string(data));
		}
	}

function _assetTransferFrom (
		address _asset,
		address _from,
		address _to,
		uint256 _idOrAmount
	) private {
		(bool success, bytes memory data) = 
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_FROM_SELECTOR,
					_from,
					_to, 
					_idOrAmount
				)
			);

		// Revert if the low-level call fails.
		if (!success) {
			revert(string(data));
		}
	}

[G2] Use nested if and, avoid multiple check combinations

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

if (citizenVaultId != 0 && vaultId != 0) {
			revert CitizenAlreadyHasVault(citizenVaultId, vaultId);

		/*
			If no optional vault is provided, and the S1 Citizen being staked already 
			has an existing Vault, override the provided `vaultId`.
		*/
		} else if (citizenVaultId != 0 && vaultId == 0) {
			citizenStatus.hasVault = true;
			vaultId = citizenVaultId;

		/*
			Otherwise, if the S1 Citizen has no component Vault, the newly-provided 
			Vault is staked and the S1 Citizen is recorded as carrying an optional, 
			separately-attached vault.
		*/
		} else if (citizenVaultId == 0 && vaultId != 0) {
			_assetTransferFrom(VAULT, msg.sender, address(this), vaultId);
			citizenStatus.hasVault = true;
			citizenStatus.stakedVaultId = vaultId;
		}

[G3] Use constants instead of type(uintx).max

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
uint256 timelockMultiplier = _timelock & type(uint128).max


it uses more gas in the distribution process and also for each transaction than constant usage
use this constant instead of type(uint128).max
uint128 constant MAX_VALUE = 2**128 -1

[G4]x = x+y is gas efficient than x += y

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
pool.totalPoints += citizenStatus.points
citizenStatus.stakedBytes += amount;
citizenStatus.points += bonusPoints;
pool.totalPoints += bonusPoints;
