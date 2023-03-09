# QA REPORT



### Summary of low risk issues


| Number     | Issue details                              | Instances |
| ------------ | -------------------------------------------- | :---------: |
| [L-1](#L1) | Use of `block.timestamp`                    |    14    |
| [L-2](#L2) | Lock pragmas to specific compiler version. |     2     |

*Total: 2 issues.*

### Summary of non-critical issues


| Number       | Issue details                                                   | Instances |
| -------------- | ----------------------------------------------------------------- | :---------: |
| [NC-1](#NC1) | Avoid whitespaces while declaring mapping variables.            |    12    |

*Total: 1 issue.*

---

### Low Risk Issues

### <a id=L1>[L-1]</a> Use of `block.timestamp`

##### Description

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the *[Entropy Illusion](https://hacken.io/discover/most-common-smart-contract-vulnerabilities/#Entropy_Illusion)* for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

##### Recommendation

Use oracle instead of `block.timestamp`

##### *Instances (14):*

File: [2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L971 )

```solidity
971: citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
1023: citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
1159: block.timestamp + timelockDuration;
1215: if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
1329: if (block.timestamp <= window.startTime) {
1331: uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;
1356: block.timestamp - lastPoolRewardTime;
1378: uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;
1433: lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;
1434: lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;
1435: lastRewardTime[_recipient][AssetType.LP] = block.timestamp;
1467: if (block.timestamp < stakedCitizen.timelockEndTime) {
1542: if (block.timestamp < stakedCitizen.timelockEndTime) {
1605: if (block.timestamp < lpPosition.timelockEndTime) {
```

### <a id=L2>[L-2]</a> Lock pragmas to specific compiler version.

##### Description

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.

##### Recommendation

Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version. [solidity-specific/locking-pragmas](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/)

##### *Instances (2):*

File: [2023-03-neotokyo/contracts/staking/BYTES2.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2 )

```solidity
2: pragma solidity ^0.8.19;
```

File: [2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2 )

```solidity
2: pragma solidity ^0.8.19;
```

### Non-critical Issues

### <a id=NC1>[NC-1]</a> Avoid whitespaces while declaring mapping variables.

##### Description

Style Guide helps to maintain code layout consistent and make code more readable.

##### Recommendation

Avoid whitespaces while declaring mapping variables.

##### *Instances (12):*

File: [2023-03-neotokyo/contracts/staking/NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L280 )

```solidity
280: mapping ( AssetType => mapping ( uint256 => uint256 )) public	timelockOptions;
312: mapping ( uint256 => RewardWindow ) rewardWindows;
316: mapping ( AssetType => PoolData ) private _pools;
319: mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
326: mapping ( uint256 => mapping ( string => string )) public identityCreditYield;
329: mapping ( string => uint256 ) public identityCreditPoints;
332: mapping ( string => uint256 ) public vaultCreditMultiplier;
372: mapping ( address => mapping( uint256 => StakedS1Citizen )) public stakedS1;
378: mapping ( address => uint256[] ) private _stakerS1Position;
405: mapping ( address => mapping( uint256 => StakedS2Citizen )) public stakedS2;
411: mapping ( address => uint256[] ) private _stakerS2Position;
434: mapping ( address => LPPosition ) public stakerLPPosition;
```
