# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1  | Using `storage` instead of `memory` for struct/array saves gas | 2 |
| 2  | `storage` variable should be cached into `memory` variables instead of re-reading them  |  5 |
| 3  | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  16  |


## Findings

### 1- Using `storage` instead of `memory` for struct/array saves gas :

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. 

The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

There are 2 instances of this issue :

File: NeoTokyoStaker.sol 

[Line 1281](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1281)
```
StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
```

[Line 1290](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1290)
```
StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];
```

### 2- `storage` variable should be cached into `memory` variables instead of re-reading them :

The instances below point to the second+ access of a state variable within a function, the caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read, thus saves **100gas** for each instance.

There are 8 instances of this issue in `NeoTokyoStaker.sol` :

[Line 1467-1472](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1467-L1472)

In the code linked above the value of `stakedCitizen.timelockEndTime` is read multiple times (3) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

[Line 1477-1478](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1477-L1478)

In the code linked above the value of `stakedCitizen.stakedBytes` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

[Line 1482-1487](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1482-L1487)

In the code linked above the value of `stakedCitizen.stakedVaultId` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

[Line 1542-1547](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1542-L1547)

In the code linked above the value of `stakedCitizen.timelockEndTime` is read multiple times (3) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

[Line 1552-1554](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1552-L1554)

In the code linked above the value of `stakedCitizen.stakedBytes` is read multiple times (2) from storage and it's value does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

### 3- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 16 instance of this issue:

```
File: NeoTokyoStaker.sol

977     pool.totalPoints += citizenStatus.points;
1029    pool.totalPoints += citizenStatus.points;
1078    citizenStatus.stakedBytes += amount;
1079	citizenStatus.points += bonusPoints;
1080	pool.totalPoints += bonusPoints;
1099    citizenStatus.stakedBytes += amount;
1100	citizenStatus.points += bonusPoints;
1101	pool.totalPoints += bonusPoints;
1160    stakerLPPosition[msg.sender].amount += amount;
1161	stakerLPPosition[msg.sender].points += points;
1164    pool.totalPoints += points;
1515    pool.totalPoints -= stakedCitizen.points;
1580    pool.totalPoints -= stakedCitizen.points;
1626    lpPosition.amount -= amount;
1627	lpPosition.points -= points;
1630    pool.totalPoints -= points;
```