# _assetTransferFrom and _assetTransfer should check if _asset is a contract
Otherwise the call will be successful if it the asset is not a contract

# Add pool view function to NeoTokyoStaker
During the audit I was forced to create these method to check the states of the pools. This methods are useful for testing but also for UX, therefore, they should be considered to be added to the staking contract

```solidity
    struct PoolInfoSummary{
		uint256 totalPoints;
		uint256 daoTax;
		uint256 rewardCount;
		RewardWindow[] rewardWindows;
	}

	struct PoolsInfoSummary{
		PoolInfoSummary s1CitizenPool;
		PoolInfoSummary s2CitizenPool;
		PoolInfoSummary lpPool;
		PoolInfoSummary bytesPool;
	}

    function getRewardWindowArray(PoolData storage pool) internal view returns(RewardWindow[] memory){
		uint256 rewardCount = pool.rewardCount;
		mapping ( uint256 => RewardWindow ) storage rewardMapping = pool.rewardWindows;
		RewardWindow[] memory rewards = new RewardWindow[](rewardCount);
		for(uint256 i; i < rewardCount; i++){
			rewards[i] = rewardMapping[i];
		}
		return rewards;
	}

    function getPoolsInfo() external view returns (PoolsInfoSummary memory){
		PoolData storage s1CitizenPool = _pools[AssetType.S1_CITIZEN];
		PoolData storage s2CitizenPool = _pools[AssetType.S2_CITIZEN];
		PoolData storage lpPool = _pools[AssetType.LP];
		PoolData storage bytesPool = _pools[AssetType.BYTES];

		PoolInfoSummary memory s1CitizenPoolInfo = PoolInfoSummary({
			totalPoints: s1CitizenPool.totalPoints,
			daoTax: s1CitizenPool.daoTax,
			rewardCount: s1CitizenPool.rewardCount,
			rewardWindows: getRewardWindowArray(s1CitizenPool)
		});

		PoolInfoSummary memory s2CitizenPoolInfo = PoolInfoSummary({
			totalPoints: s2CitizenPool.totalPoints,
			daoTax: s2CitizenPool.daoTax,
			rewardCount: s2CitizenPool.rewardCount,
			rewardWindows: getRewardWindowArray(s2CitizenPool)
		});

		PoolInfoSummary memory lpPoolInfo = PoolInfoSummary({
			totalPoints: lpPool.totalPoints,
			daoTax: lpPool.daoTax,
			rewardCount: lpPool.rewardCount,
			rewardWindows: getRewardWindowArray(lpPool)
		});

		PoolInfoSummary memory bytesPoolInfo = PoolInfoSummary({
			totalPoints: bytesPool.totalPoints,
			daoTax: bytesPool.daoTax,
			rewardCount: bytesPool.rewardCount,
			rewardWindows: getRewardWindowArray(bytesPool)
		});
		
		PoolsInfoSummary memory poolsInfo = PoolsInfoSummary({
			s1CitizenPool: s1CitizenPoolInfo,
			s2CitizenPool: s2CitizenPoolInfo,
			lpPool: lpPoolInfo,
			bytesPool: bytesPoolInfo
		});

		return poolsInfo;
	}
```


# DOS to getPoolReward if enough rewards are added to a pool
If enough rewards are set for a pool, block gas would be reached when `getPoolReward` is called, DOSing `claimReward` function, forbidding stakers from claiming their corresponding rewards.

Currently, pool rewards can be defined by `configurePools` function by an address with corresponding permission. It would be advisable to add a max number of rewards check to add to a pool, in order to avoid DOS `getPoolReward` function.

# NeoTokyoStaker assumes LP decimals are 18
Even though nowadays every LP contract has it decimals set to 18, it would be advisable to allow setting this value, given that LP can be set again through `configureLP` function, in case someday the protocol decide to use an unconventional type of LP. This would also means adding a new variable called `lpDecimals` to change whenever `LP` changes.

# withdraw: Should check that `uint8(_assetType) > 3` instead of `uint8(_assetType) > 4`
Given there is no case for 4 defined below, 4 should also be taken into account to revert the function. This means:
```diff
    // NeoTokyoStaker.withdraw
-   if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
+   if (uint8(_assetType) == 2 || uint8(_assetType) > 3) {
        revert InvalidAssetType(uint256(_assetType));
    }
```

# `VAULT_CAP`, `LP` and `NO_VAULT_CAP` should follow conventional naming
Variable which are not constants/immutables should be named following lower camel case naming convention