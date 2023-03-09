# GAS OPTIMIZATION REPORT

### Summary of optimizations


| Number     | Issue details                                                            | Instances |
| ------------ | -------------------------------------------------------------------------- | :---------: |
| [G-1](#G1) | Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead. |     7     |
| [G-2](#G2) | Multiple accesses of a mapping/array should use a local variable cache.  |     7     |
| [G-3](#G3) | `>=` costs less gas than `>`.                                            |     8     |
| [G-4](#G4) | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables   |    22    |
| [G-5](#G5) | Using fixed bytes is cheaper than using `string`.                         |     7     |

*Total: 5 issues.*

---

### Gas Optimizations

### <a id=G1>[G-1]</a> Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead.

##### Description

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

##### Recommendation

Use `uint256` instead.

##### *Instances (7):*

File: [2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L291 )

```solidity
291: uint128 startTime;
292: uint128 reward;
963: uint256 timelockMultiplier = _timelock & type(uint128).max;
1017: uint256 timelockMultiplier = _timelock & type(uint128).max;
1141: uint256 timelockMultiplier = _timelock & type(uint128).max;
1205: if (uint8(_assetType) > 4) {
1668: if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
```

### <a id=G2>[G-2]</a> Multiple accesses of a mapping/array should use a local variable cache.

##### Description

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata. [Similar findings](https://github.com/code-423n4/2022-06-infinity-findings/issues/186)

##### Recommendation

Use a local variable cache.

##### *Instances (7):*

File: [2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1823 )

```solidity
1823: uint256 poolRewardWindowCount = _inputs[i].rewardWindows.length;
1824: _pools[_inputs[i].assetType].rewardCount = poolRewardWindowCount;
1825: _pools[_inputs[i].assetType].daoTax = _inputs[i].daoTax;
1830: _pools[_inputs[i].assetType].rewardWindows[j] =
1831: _inputs[i].rewardWindows[j];
1834: if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
1837: lastTime = _inputs[i].rewardWindows[j].startTime;
```

### <a id=G3>[G-3]</a> `>=` costs less gas than `>`.

##### Description

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which [saves 3 gas](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

##### Recommendation

Use `>=` instead if appropriate.

##### *Instances (8):*

File: [2023-03-neotokyo/contracts/staking/BYTES2.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L123 )

```solidity
123: if (reward > 0) {
126: if (daoCommision > 0) {
```

File: [2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1066 )

```solidity
1066: if (citizenStatus.stakedBytes + amount > cap) {
1087: if (citizenStatus.stakedBytes + amount > cap) {
1205: if (uint8(_assetType) > 4) {
1477: if (stakedCitizen.stakedBytes > 0) {
1552: if (stakedCitizen.stakedBytes > 0) {
1668: if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
```

### <a id=G4>[G-4]</a> `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

##### Description

Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

##### Recommendation

Use `<x> = <x> + <y>` or `<x> = <x> -<y>` instead.

##### *Instances (22):*

File: [2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L977 )

```solidity
977: pool.totalPoints += citizenStatus.points;
1029: pool.totalPoints += citizenStatus.points;
1078: citizenStatus.stakedBytes += amount;
1079: citizenStatus.points += bonusPoints;
1080: pool.totalPoints += bonusPoints;
1099: citizenStatus.stakedBytes += amount;
1100: citizenStatus.points += bonusPoints;
1101: pool.totalPoints += bonusPoints;
1160: stakerLPPosition[msg.sender].amount += amount;
1161: stakerLPPosition[msg.sender].points += points;
1164: pool.totalPoints += points;
1283: points += s1Citizen.points;
1292: points += s2Citizen.points;
1298: points += stakerLPPosition[_recipient].points;
1332: totalReward += currentRewardRate * timeSinceReward;
1343: totalReward += currentRewardRate * timeSinceReward;
1357: totalReward += currentRewardRate * timeSinceReward;
1515: pool.totalPoints -= stakedCitizen.points;
1580: pool.totalPoints -= stakedCitizen.points;
1626: lpPosition.amount -= amount;
1627: lpPosition.points -= points;
1630: pool.totalPoints -= points;
```

### <a id=G5>[G-5]</a> Using fixed bytes is cheaper than using `string`.

##### Description

As a rule of thumb, use bytes for arbitrary-length raw byte data and `string` for arbitrary-length string (UTF-8) data. If you can limit the length to a certain number of bytes, always use one of `bytes1` to `bytes32` because they are much cheaper..

##### Recommendation

Use fixed bytes instead of `string`.

##### *Instances (7):*

File: [2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L628 )

```solidity
628: ) public view returns (string memory) {
639: string memory vaultMultiplier = (_vaultId != 0)
665: string memory vaultMultiplier = (_vaultId != 0)
825: string memory _a,
826: string memory _b
939: string memory citizenCreditYield = getCreditYield(
949: string memory class = IGenericGetter(IDENTITY).getClass(identityId);
```
