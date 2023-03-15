# [G-01] Non optimal iteration over S1Citizen and S2Citizen

Optimize `getPoolReward` function.
In current implementation we iterate over whole array of StakedS1Citizen items for _recipient to calculate points.
Like here:
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1279-L1286
and here:
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1288-L1295

But we can use additional mapping: `mapping(address => uint256) pointStakedS1Citizen` (same for S2Citizen), where placed total points per user. This mapping must be updated when we:
* Adding new token for user. 
* Adding new BYTES2 token for user. 
* Removing token from staking.

And therefore, to calculate total user's points in the function `getPoolReward` we can just read value from the storage.

This optimization needed, because adding/deletion token is rare while `getPoolReward` might be called often. 

# [G-02] Non optimal iteration over windows

Optimize `getPoolReward` function.
In current implementation we iterate over whole list of windows. But, by design -- rewards amount increases during window iteration. Time complexity here is O(n). But we can improve this complexity to O(log(N)) use binary search, and storing in windows sum on prefixes.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1312-L1384

So, in improvement version we do next:
1) Find the first unprocessed window (use binary search by startTime, correct consider corner cases).
2) Go to the last window, subtract cumulative amount i.e. `last_window.cumulative_amount - first_unprocessed_window.cumulative_amount`.
3) To calculate cumulative amount required during insertion in the function `configurePools` sum-up windows and write cumulative value or on the admin side pre-calculate required values and pass them 
directly.

This optimization needed, because `getPoolReward` might be called very often, and it's not needed to iterate over whole windows each time.  


