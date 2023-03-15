DOS in getPoolReward

    1320  uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;


If i =  0 , it could be reverted this function since 0-1 = -1 .

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1320


## Lack of checks for Address(0) and Unit 0 . 
The following methods have a lack of checks if the received argument is an address, it’s good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than address(0) and uint 0.

Affected Source Code

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588


https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1709
