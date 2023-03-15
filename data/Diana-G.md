
| S No. | Issue | Instances | Gas Savings (from provided tests) |
|-----|-----|-----|-----|
| [G-01] | x += y costs more gas than x = x + y for state variables (same applies for x -= y) | 22 | 2486  
| [G-02] | Expressions for constant values such as a call to keccak256(), should use immutable rather than constant | 7 | -

-------------

## G-01 x += y costs more gas than x = x + y for state variables (same applies for x -= y)

Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**.

_There are 22 instances of this issue_

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

```
File: contracts/staking/NeoTokyoStaker.sol

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

-----------

## G-02 Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detail description of the issue.

_There are 7 instances of this issue_

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol

```
File: contracts/staking/BYTES2.sol

37: bytes32 public constant BURN = keccak256("BURN");
40: bytes32 public constant ADMIN = keccak256("ADMIN");
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

```
File: contracts/staking/NeoTokyoStaker.sol

206: bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
209-211: bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);
214: bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
217: bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
220: bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```

----------
