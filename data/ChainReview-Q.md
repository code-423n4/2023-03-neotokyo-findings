# [01] Missing zero address validation

There are no zero address validation checks implemented for the constructor as well as the configuration functions, which could lead to loss of funds or the contact not working as expected.

Note that it may be a bit excessive to add this check for all the functions, but for some configuration functions it definitely makes sense as it prevents malicious behaviour as well as input errors.

To prevent this, simply add the following line before the addresses are being initialized :

    require(variable!= address(0), "zero address initialization");

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-L85

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

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L165

	function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}


https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L176

	function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L606

	constructor (
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

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1708-L1715

	function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
		LP = _lp;
	}

# [02] Reordering of Multiplications and Divisions can lead to precision loss

Solidity's integer division truncates and therefore performing division before multiplication can lead to precision loss.

For example, $a/b * c/d$ will equal to 0 if either $b>a$ or $d>c$, but it would not be necessarily be the case when using $(a*c) / (b*d)$ instead.

In the `_stakeLP()` function for example, if the amount is less then `1e16`, the result will equal 0. The code could be rewritten as 

    uint256 points = (amount * 100 * timelockMultiplier) / (1e18 * _DIVISOR);

The same reasoning applies for `getPoolReward()` 

    uint256 share = (points * _PRECISION * totalReward) / pool.totalPoints ;

and `_withdrawLP()` :

    uint256 points = (amount * 100  * lpPosition.multiplier) / (_DIVISOR * 1e18);

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1152-L1165

         // Update caller staking information and asset data.
		PoolData storage pool = _pools[AssetType.LP];
		unchecked {
			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

			// Update the caller's LP token stake.
			stakerLPPosition[msg.sender].timelockEndTime =
				block.timestamp + timelockDuration;
			stakerLPPosition[msg.sender].amount += amount;
			stakerLPPosition[msg.sender].points += points;

			// Update the pool point weights for rewards.
			pool.totalPoints += points;
		}

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1386-L1393

		// Return final shares.
			unchecked {
				uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
				uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
				share /= _PRECISION;
				daoShare /= _PRECISION;
				return ((share - daoShare), daoShare);
			}

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1620-L1631

	// Update caller staking information and asset data.
		PoolData storage pool = _pools[AssetType.LP];
		unchecked {
			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

			// Update the caller's LP token stake.
			lpPosition.amount -= amount;
			lpPosition.points -= points;

			// Update the pool point weights for rewards.
			pool.totalPoints -= points;
		}