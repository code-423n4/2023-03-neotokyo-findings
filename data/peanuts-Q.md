## Table of Contents

- [L-01] Lack of check for address(0) value input
- [L-02] Avoid division before multiplication
- [L-03] The points multiplier may be too small, resulting in 0 points for small deposits
- [L-04] Unbounded loops may cause DoS due to exceeded gas limits
- [L-05] Risky usage of lockLP()
- [N-01] Unchecked pragma version
- [N-02] Use a stable pragma version
- [N-03] Event is missing indexed fields


### [L-01] Lack of check for address(0) value input

In the BYTES2.sol constructor, the addresses passed into the constructor is not checked for 0 value. It is always good practice to have zero address checks in case of accidental input error. 

```
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

```
	constructor (
		address _bytes,
		address _s1Citizen,
		address _staker,
		address _treasury
	) {
+		require(_bytes != address(0), "Address Zero check")
+		require(_s1Citizen != address(0), "Address Zero check")
+		require(_staker != address(0), "Address Zero check")
+		require(_treasury != address(0), "Address Zero check")

		BYTES1 = _bytes;
		S1_CITIZEN = _s1Citizen;
		STAKER = _staker;
		TREASURY = _treasury;
	}

This also applies to function like `changeStakingContractAddress()` and `changeTreasuryContractAddress()`. Check the address input is not zero.

```

### [L-02] Avoid division before multiplication

NeoTokyoStaker.sol has some calculations with division that comes before multiplication.
```
1155:		uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
```

```
1623:		uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
```

In this instance, the share value is calculated by multiplying points and precision, dividing by the pool.totalPoints and multiplying by total reward and dividing by _PRECISION again. This may lead to a truncation of 0.

```
1388:		uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
1389:		uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
1390:		share /= _PRECISION;
1391:		daoShare /= _PRECISION;
```

For example, given arbitrary numbers, lets say a user has 1 point.

```
		uint256 share = points * _PRECISION / pool.totalPoints * totalReward / _PRECISION (continued in line 1390);
		uint256 share = 1 * 1e12 / 1e13 * 1e18 / 1e12 = 0 (when it is supposed to be 100_000, because 1e12 / 1e13 truncates to 0)
```

Try to avoid division before multiplication to avoid accidental truncation issues. Multiply every thing first before dividing, like so:
```
- 1155:	uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
+ 1155:	uint256 points = amount * 100 * timelockMultiplier / 1e18 / _DIVISOR;
```

For the shares, can be rewritten as such so that the share need not pass through division before multiplication. 
 
```
1388:		uint256 share = points * _PRECISION * totalReward / pool.totalPoints / _PRECISION;
1389:		uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
1391:		daoShare /= _PRECISION;
```

Reference:

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1155

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1623

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1390-L1391

### [L-03] The points multiplier may be too small, resulting in 0 points for small deposits

If a user stakes 1 byte into an S1/S2 citizen, he would receive 0 points instead of 0.005 points. The bonus points is calculated by taking the amount, multiplying by a 100 and dividing by _BYTES_PER_POINT. 200 bytes make a point.

```
1077:		uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
```
```
202:		/// The number of BYTES needed to get one point in BYTES staking calculations.
203:		uint256 constant private _BYTES_PER_POINT = 200 * 1e18;
```

If the user stakes 1 byte only, amount is 1e18 and the calculationg is 1e18 * 100 / 200 * 1e18 which is 0.5 and truncates to 0. Since 100 = 1 point, 0.5 = 0.005 point but in this case its 0 points. If this is intended to dissuade low staking amounts then its understandable but if its not intended behaviour then the multipler of 100 should be larger (10000 or 1e18)

### [L-04] Unbounded loops may cause DoS due to exceeded gas limits

If a user has many S1/S2 citizens to stake in the contract, then there will be many loop iterations when `getPoolReward()` or `_withdrawS1Citizen()` is called, which may cause a DoS due to gas limit. Consider implementing a index range to prevent any unbounded loop error. Low issue since it user may need hundreds or even thousands of staked citizens to cause the error, and the likelihood of a user staking so many citizens is low. Nevertheless, still a good precautionary measure for whales.

```
	if (_assetType == AssetType.S1_CITIZEN) {
		for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
			uint256 citizenId = _stakerS1Position[_recipient][i];
			StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
			unchecked {
				points += s1Citizen.points;
				i++;
			}
```

### [L-05] Risky usage of lockLP()

Having a function that permanently changes a contract is pretty risky. If lockLP() is called, then the lp address cannot be changed forever. In case of a hack or upgradeable contracts in the future, the lp adress cannot be migrated or changed. Recommend having a temporary locking measure and allowing permitted users to unlock the LP if need be. 

```
	function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
		LP = _lp;
	}


	/**
		This function allows a permitted user to forever prevent alteration of the 
		LP token contract address.
	*/
	function lockLP () external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		lpLocked = true;
	}
```

Recommend changing the lockLP to allow a bool value instead.

```
	function lockLP (bool lock) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		lpLocked = lock;
	}
```

### [N-01] Unchecked pragma version

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

```
- pragma solidity ^0.8.19;
+ pragma solidity 0.8.19;
```

### [N-02] Use a stable pragma version

Since v 0.8.19 is the newest, it may not be extensively tested and may contain unknown errors. Use a more stable version like 0.8.17 instead.


```
- pragma solidity 0.8.19;
+ pragma solidity 0.8.17;
```

### [N-03] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields.

```
	event Stake (
		address indexed staker,
		address indexed asset,
-		uint256 timelockOption,
+		uint256 indexed timelockOption,
		uint256 amountOrTokenId
	);


	/**
		This event is emitted each time a recipient claims a reward.

		@param recipient The recipient of the reward.
		@param reward The amount of BYTES rewarded to the `recipient`.
		@param tax The amount of BYTES minted as tax to the DAO.
	*/
	event Claim (
		address indexed recipient,
-		uint256 reward,
-		uint256 tax
+		uint256 indexed reward,
+		uint256 indexed tax
	);


	/**
		This event is emitted when an asset is successfully withdrawn.

		@param caller The address of the caller who withdrew an `asset`.
		@param asset The address of the asset being withdrawn.
		@param amountOrTokenId The amount of `asset` withdrawn or, for S1 and S2 
			Citizens, the ID of the specific token withdrawn.
	*/
	event Withdraw (
		address indexed caller,
		address indexed asset,
-		uint256 amountOrTokenId 
+		uint256 indexed amountOrTokenId 
	);
```

