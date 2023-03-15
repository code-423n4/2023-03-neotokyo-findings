# Gas Optimization

# Summary

| Number | Optimization Details                                                                                     | Context |
| :----: | :------------------------------------------------------------------------------------------------------- | :-----: |
| [G-01] | EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT |    7    |
| [G-02] | SETTING THE CONSTRUCTOR TO PAYABLE                                                                       |    2    |
| [G-03] | USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS                 |   10    |
| [G-04] | SHOULD USE ARGUMENTS INSTEAD OF STATE VARIABLE                                                           |    6    |
| [G-05] | The result of function calls should be cached rather than re-calling the function                        |    1    |
| [G-06] | With assembly, .call (bool success)  transfer can be done gas-optimized                                  |    2    |
| [G-07] | Duplicated require()/if() checks should be refactored to a modifier or function                          |    8    |
| [G-08] | Use constants instead of type(uintx).max                                                                 |    3    |
| [G-09] | Use nested if and, avoid multiple check combinations                                                     |    4    |
| [G-10] | Use assembly to write address storage values                                                             |   11    |

## [G-01] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

### Details

There are **7** instances of this issue.

### Github Permalinks

```solidity
File:  /main/contracts/staking/BYTES2.sol

37 	bytes32 public constant BURN = keccak256("BURN");


40	bytes32 public constant ADMIN = keccak256("ADMIN");
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

206	 bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");


209	 bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
210 		"CONFIGURE_TIMELOCKS"
211  	);


214  bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");


217	 bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");


220	 bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

### Recommendation Code

```solidity
File:/main/contracts/staking/BYTES2.sol

-37 	bytes32 public constant BURN = keccak256("BURN");

+       bytes32 public immutable BURN = keccak256("BURN");
```

## [G-02] Setting the constructor to payable

### Summary

Saves ~13 gas per instance

### Details

There are **2** instance of this issue

### Github Permalinks

```solidity
File:  /main/contracts/staking/BYTES2.sol

75	constructor (
76		address _bytes,
77		address _s1Citizen,
78		address _staker,
79		address _treasury
80	) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-85

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

588	constructor (
589		address _bytes,
590		address _s1Citizen,
591		address _s2Citizen,
592		address _lpToken,
593		address _identity,
594		address _vault,
595		uint256 _vaultCap,
596		uint256 _noVaultCap
597	) {
```

### Recommendation Code

```solidity
File:  /main/contracts/staking/BYTES2.sol

75	constructor (
76		address _bytes,
77		address _s1Citizen,
78		address _staker,
79		address _treasury
-80	) {

+  ) payable {
```

## [G-03] USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS

### Summary

calldata must be used when declaring an external function's dynamic parameters

### Details

When a function with a memory array is called externally, the abi.decode ()  step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 \* <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

There are **10** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

1739		uint256[] memory _timelockIds,

1740		uint256[] memory _encodedSettings

1761		uint256[] memory _citizenRewardRates,

1762		string[] memory _vaultRewardRates,

1763		string[] memory _identityCreditYields

1784		string[] memory _identityCreditYields,

1785		uint256[] memory _points

1803 		string[] memory _vaultCreditMultipliers,

1804		uint256[] memory _multipliers

1820		PoolConfigurationInput[] memory _inputs
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

### Recommendation Code

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

-1739		uint256[] memory _timelockIds,

+           uint256[] calldata _timelockIds,
```

## [G-04] SHOULD USE ARGUMENTS INSTEAD OF STATE VARIABLE

### Summary

state variables should not used in emit

### Details

There are **6** instances of this issue.

### Github Permalinks

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

226	address immutable public S1_CITIZEN;  // S1_CITIZEN    SV


981   	emit Stake
982			msg.sender,
983:		S1_CITIZEN,
984			_timelock,
985			citizenId
986		);


229 	address immutable public S2_CITIZEN; // S2_CITIZEN  SV

1033    	emit Stake(
1034			msg.sender,
1035:			S2_CITIZEN,
1036			_timelock,
1037			citizenId
1038	    	);


232	    address public LP; // LP   SV

1168		emit Stake(
1169			msg.sender,
1170:			LP,
1172			_timelock,
1173			amount
1174		);

1524		emit Withdraw(
1525			msg.sender,
1526:			S1_CITIZEN,
1527			citizenId
1528		);

1587	emit Withdraw(
1588			msg.sender,
1589:			S2_CITIZEN,
1590			citizenId
1591		);


1639	emit Withdraw(
1640			msg.sender,
1641			LP,
1642			amount
1643		);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

## [G-05] The result of function calls should be cached rather than re-calling the function

### Summary

### Details

There is **1** instance of this issue.

### Github Permalinks

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

950 			if (_stringEquals(class, "Hand of Citadel")) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

## [G-06] With assembly, .call (bool success)  transfer can be done gas-optimized

### Summary

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.

https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19

### Details

There is **2** instance of this issue.

### Github Permalinks

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

772 (bool success, bytes memory data) =
773			_asset.call(
774				abi.encodeWithSelector(
775					_TRANSFER_FROM_SELECTOR,
776					_from,
777					_to,
778					_idOrAmount
779				)
780			);



801  (bool success, bytes memory data) =
802	 		_asset.call(
803				abi.encodeWithSelector(
804					_TRANSFER_SELECTOR,
805					_to,
806					_amount
807				)
808			);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

## [G-07] Duplicated require()/if() checks should be refactored to a modifier or function

### Summary

### Details

There are **8** instances of this issue

### Github Permalinks

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

693		    if (_assetType == AssetType.S1_CITIZEN) {
1278		if (_assetType == AssetType.S1_CITIZEN) {


783		    if (!success) {
811		    if (!success) {

1066		if (citizenStatus.stakedBytes + amount > cap) {
1087		if (citizenStatus.stakedBytes + amount > cap) {

1071		if (citizenStatus.timelockEndTime == 0) {
1092		if (citizenStatus.timelockEndTime == 0) {

1467		if (block.timestamp < stakedCitizen.timelockEndTime) {
1542		if (block.timestamp < stakedCitizen.timelockEndTime) {

1472		if (stakedCitizen.timelockEndTime == 0) {
1547		if (stakedCitizen.timelockEndTime == 0) {

1477		if (stakedCitizen.stakedBytes > 0) {
1552		if (stakedCitizen.stakedBytes > 0) {

1502		if (citizenId == oldPosition[stakedIndex]) {
1567		if (citizenId == oldPosition[stakedIndex]) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

## [G-08] Use constants instead of type(uintx).max

### Summary

type(uintx).max it uses more gas in the distribution process and also for each transaction than constant usage.

### Details

there are **3** instances of this issue

### Github Permalinks

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

963		   uint256 timelockMultiplier = _timelock & type(uint128).max;

1017		uint256 timelockMultiplier = _timelock & type(uint128).max;

1141		uint256 timelockMultiplier = _timelock & type(uint128).max;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

## [G-09] Use nested if and, avoid multiple check combinations

### Summary

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

### Details

there are **4** instances of this issue

### Github Permalinks

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

910		if (citizenVaultId != 0 && vaultId != 0) {

917		} else if (citizenVaultId != 0 && vaultId == 0) {

926		} else if (citizenVaultId == 0 && vaultId != 0) {

1834 				if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

### Recomendation Code

```solidity
File: /src/LotteryMath.sol

-910:    if (citizenVaultId != 0 && vaultId != 0) {

+       if(citizenVaultId != 0) {
+          if(vaultId != 0){
            }

           ...}
```

## [G-10] Use assembly to write address storage values

### Details

there are **11** instances of this issue

### Github Permalinks

```solidity
File: /contracts/staking/BYTES2.sol

81		BYTES1 = _bytes;

82		S1_CITIZEN = _s1Citizen;

83		STAKER = _staker;

84		TREASURY = _treasury;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L81-84

```solidity
File: /contracts/staking/NeoTokyoStaker.sol

598		BYTES = _bytes;

599		S1_CITIZEN = _s1Citizen;

600		S2_CITIZEN = _s2Citizen;

601		LP = _lpToken;

602		IDENTITY = _identity;

603		VAULT = _vault;

1714		LP = _lp;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

### Recommendation Code

```solidity
File: /contracts/staking/BYTES2.sol

75   constructor (
76	    	address _bytes,
            ...
80            )  {
                assembly {
                    sstore(BYTES1.slot,_bytes)
                }
            }
```
