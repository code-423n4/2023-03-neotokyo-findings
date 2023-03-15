## Non Critical Issues

### [N-1] Missing checks for `address(0)` when assigning values to address state variables

*Find (6) instance(s) in contracts*:
```solidity
File: staking/BYTES2.sol

83: 		STAKER = _staker;

84: 		TREASURY = _treasury;

165: 		STAKER = _staker;

176: 		TREASURY = _treasury;

```

```solidity
File: staking/NeoTokyoStaker.sol

601: 		LP = _lpToken;

1714: 		LP = _lp;

```

### [N-2] Use of block.timestamp
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
#### Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers — i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

Instances where block.timestamp is used:

*Find (14) instance(s) in contracts*:
```solidity
File: staking/NeoTokyoStaker.sol

971: 			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

1023: 			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

1159: 				block.timestamp + timelockDuration;

1215: 		if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {

1329: 						if (block.timestamp <= window.startTime) {

1331: 								uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;

1356: 										block.timestamp - lastPoolRewardTime;

1378: 						uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;

1433: 		lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;

1434: 		lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;

1435: 		lastRewardTime[_recipient][AssetType.LP] = block.timestamp;

1467: 		if (block.timestamp < stakedCitizen.timelockEndTime) {

1542: 		if (block.timestamp < stakedCitizen.timelockEndTime) {

1605: 		if (block.timestamp < lpPosition.timelockEndTime) {

```

### [N-3] Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*Find (4) instance(s) in contracts*:
```solidity
File: staking/BYTES2.sol

61: 	event BytesUpgraded (

```

```solidity
File: staking/NeoTokyoStaker.sol

539: 	event Stake (

553: 	event Claim (

567: 	event Withdraw (

```

### [N-4] Constant values such as a call to keccak256(), should used to immutable rather than constant
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.
  
Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

*Find (8) instance(s) in contracts*:
```solidity
File: staking/BYTES2.sol

37: 	bytes32 public constant BURN = keccak256("BURN");

40: 	bytes32 public constant ADMIN = keccak256("ADMIN");

```

```solidity
File: staking/NeoTokyoStaker.sol

203: 	uint256 constant private _BYTES_PER_POINT = 200 * 1e18;

206: 	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

209: 	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(

214: 	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

217: 	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

220: 	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```


## Low Issues

### [L-1] Loss of precision due to rounding
#### Recommendation
Multiplication should always be performed before division to avoid loss of precision.

*Find (3) instance(s) in contracts*:
```solidity
File: staking/NeoTokyoStaker.sol

1155: 			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

1389: 				uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);

1623: 			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

```

### [L-2] Non-library/interface files should use fixed compiler versions, not floating ones

*Find (2) instance(s) in contracts*:
```solidity
File: staking/BYTES2.sol

2: pragma solidity ^0.8.17;

```

```solidity
File: staking/NeoTokyoStaker.sol

2: pragma solidity ^0.8.17;

```