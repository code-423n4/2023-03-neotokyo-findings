# Use of Floating pragma

The contracts should be deployed with the same solidity version with which they have been tested. Locking the pragma ensures that the contracts don't get deployed with an older version which might introduce bugs.

Recommended Mitigation Steps
Use fixed pragma


# Use all multiplication before division

Dividing before all multiplication has been done can lead to lack of precision. Therefore, the following should be changed

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1388

into 				uint256 share = points * _PRECISION * totalReward / pool.totalPoints;