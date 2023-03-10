Use Custom Errors to save gas
---

Sample case: 
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L784
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L775


` revert(string(data));` Could be long or short depending on the response. A standard custom error here will save gas


Use less strict inequalities 
---
Sample case: 
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1467

When inside the if statements, non-strict inequalities (>=, <=) are usually cheaper than the strict equalities (>, <).

Array Length Loop Caching 
--
Example 
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L717


Mitigation: 

uint256 stakePositionLength =  _stakerS1Position[_staker].length

for (uint256 i; i < stakePositionLength; ) {



During each iteration of the loop, reading the length of the array uses more gas than is necessary. In the most favorable scenario, in which the length is read from a memory variable, storing the array length in the stack can save about 3 gas per iteration. In the least favorable scenario, in which external calls are made during each iteration, the amount of gas wasted can be significant.

Cheaper Conditional Operations
--

Sample case can be found here: https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L639

`string memory vaultMultiplier = (_vaultId > 0)` is cheaper than `string memory vaultMultiplier = (_vaultId != 0)` 


