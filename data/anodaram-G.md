# Too long code in `getPoolReward`

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1312-L1384

It takes 70+ lines for calculating `totalReward`.
We can use this short code, which has lower gas also.
Also, it is easy to understand.

```
			for (uint256 i; i < windowCount; ) {
				uint256 startTime = pool.rewardWindows[i].startTime;
				if (startTime < lastPoolRewardTime) {
					startTime = lastPoolRewardTime;
				}
				uint256 endTime = block.timestamp;
				if (i < windowCount - 1 && endTime > pool.rewardWindows[i + 1].startTime) {
					endTime = pool.rewardWindows[i + 1].startTime;
				}
				if (endTime > startTime) {
					totalReward += (endTime - startTime) * pool.rewardWindows[i].reward;
				}
				unchecked { i++; }
			}
```