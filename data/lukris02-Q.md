# QA Report for Neo Tokyo contest
## Overview
During the audit, 2 low and 2 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | If ```reward``` and ```daoCommision``` are equal to zero, revert ```getReward()``` function. | Low | 1
L-2 | ```data.length``` is not checked | Low | 2
NC-1 | Order of Functions | Non-Critical | 6
NC-2 | Wrong modifier order in  function declaration | Non-Critical | 1

## Low Risk Findings(2)
### L-1. If ```reward``` and ```daoCommision``` are equal to zero, revert ```getReward()``` function.
##### Description
If ```(reward == 0 && daoCommision == 0)```, it is better to revert the function ```getReward()```.
##### Instances
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114-L129

##### Recommendation
Add:
```
if (reward == 0 && daoCommision == 0) {
revert ZeroRewardAndDaoCommision;
}
```
#
### L-2. ```data.length``` is not checked
##### Description
There is no check that ```data.length == 0```.
##### Instances
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L783-L785
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L811-L813

##### Recommendation
Add:
```
require(data.length == 0 || abi.decode(data, (bool)), "Transfer failed");
```
#
## Non-Critical Risk Findings(2)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
Public functions should be placed after external:
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L625
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L659

External and public functions should be placed before private:
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1264
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1409 private)
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1659

##### Recommendation
Reorder functions where possible.
#
### NC-2. Wrong modifier order in  function declaration
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.19/style-guide.html#function-declaration), the modifier order for a function should be:
1. Visibility
2. Mutability
3. Virtual
4. Override
5. Custom modifiers

##### Instances
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L143

##### Recommendation
Change ```hasValidPermit(UNIVERSAL, BURN) external``` to ```external hasValidPermit(UNIVERSAL, BURN)```.