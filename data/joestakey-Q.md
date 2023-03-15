# QA Report


## Low Issues


| |Issue|
|-|:-|
| [L-1](#L-1) | Loss of precision |
| [L-2](#L-2) | Wrong error logic of `assetType` in `stake` |
| [L-3](#L-3) | Wrong error logic of `startTime` in `stake` |
| [L-4](#L-4) | `_withdrawLP` should reset `timelockEndTime` |



### <a name="L-1"></a>[L-1] Loss of precision

When staking `LP` tokens, there is a loss of precision in the computation of `points` due to a division before multiplication


```
File: contracts/staking/NeoTokyoStaker.sol
1154: 		unchecked {
1155: 			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
```

The loss is generally minor and happens only if `timelockMultiplier > 100`.
The only case where the loss is significant is when `amount < 1e16`, which depending on the state of the `BYTES/ETH` pool, may be of value greater than $1. In such case, the `points` computed are 0, meaning the user share of the pool is `0`.

Refactor as follows:

```diff

File: contracts/staking/NeoTokyoStaker.sol
1154: 		unchecked {
-1155: 			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
+1155: 			uint256 points = amount * 100 * timelockMultiplier / 1e18 / _DIVISOR;
```

### <a name="L-2"></a>[L-2] Wrong error logic of `assetType` in `stake`

The function reverts if `assetType > 4`. This should be `>=`, as `4` is not a valid type.


```solidity
File: staking/NeoTokyoStaker.sol

1205: 		if (uint8(_assetType) > 4) { //@audit-info should be >=, 4 is not a valid type
      			revert InvalidAssetType(uint256(_assetType));
      		}
```

### <a name="L-3"></a>[L-3] Wrong error logic of `startTime` in `stake`

The function reverts if `_pools[_assetType].rewardWindows[0].startTime >= block.timestamp`. This should be `>`, as currently the function reverts when the `startTime` is reached at the `block.timestamp`.


```solidity
File: staking/NeoTokyoStaker.sol

1214: 		// Validate that the asset being staked matches an active pool.
1215: 		if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp)
1216: 			revert InactivePool(uint256(_assetType));
1217: 		}

```



### <a name="L-4"></a>[L-4] `_withdrawLP` should reset `timelockEndTime`

This does not create any vulnerability, but may make it confusing when a user calls `getStakerPositions` if they have withdrawn their `LP`.
Note that resetting `timelockEndTime` is done for the the citizen staking functions

```diff
File: staking/NeoTokyoStaker.sol

1634: 		if (lpPosition.amount == 0) {
1635: 			lpPosition.multiplier = 0;
+						lpPosition.timelockEndTime = 0
1636: 		}

```




## Non Critical Issues


| |Issue|
|-|:-|
| [NC-1](#NC-1) | Capital letters should be reserved for constants |
| [NC-2](#NC-2) | Constants on the left are better |
| [NC-3](#NC-3) | Scientific Notation |
| [NC-4](#NC-4) | Imports should be cleaner |


### <a name="NC-1"></a>[NC-1] Capital letters should be reserved for constants


```solidity
File: staking/BYTES2.sol

49: 	address public STAKER;

52: 	address public TREASURY;
```

### <a name="NC-2"></a>[NC-2] Constants on the left are better
This is [safer in case you forget to put a double equals sign](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html), as the compiler will throw an error instead of assigning the value to the variable.


```solidity
File: staking/NeoTokyoStaker.sol

633: 		if (rewardRate == 0) {

693: 		if (_assetType == AssetType.S1_CITIZEN) {

695: 		} else if (_assetType == AssetType.S2_CITIZEN) {

917: 		} else if (citizenVaultId != 0 && vaultId == 0) {

926: 		} else if (citizenVaultId == 0 && vaultId != 0) {

947: 		if (handClaimant == 1) {

1060: 		if (seasonId == 1) {

1071: 			if (citizenStatus.timelockEndTime == 0) {

1084: 		} else if (seasonId == 2) {

1092: 			if (citizenStatus.timelockEndTime == 0) {

1144: 		if (stakerLPPosition[msg.sender].multiplier == 0) {

1210: 		if (_pools[_assetType].rewardCount == 0) {

1221: 		if (timelockOption == 0) {

1278: 			if (_assetType == AssetType.S1_CITIZEN) {

1287: 			} else if (_assetType == AssetType.S2_CITIZEN) {

1296: 			} else if (_assetType == AssetType.LP) {

1301: 				revert InvalidAssetType(uint256(_assetType));

1320: 					uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;

1353: 							if (j == windowCount - 1) {

1376: 				} else if (i == windowCount - 1) {

1472: 		if (stakedCitizen.timelockEndTime == 0) {

1502: 			if (citizenId == oldPosition[stakedIndex]) {

1547: 		if (stakedCitizen.timelockEndTime == 0) {

1567: 			if (citizenId == oldPosition[stakedIndex]) {

1634: 		if (lpPosition.amount == 0) {

1697: 		_withdraw();

```


### <a name="NC-3"></a>[NC-3] Scientific Notation
It is good practice to use scientific notation instead of using exponents or explicitly writing number of high decimals


```solidity
File: staking/NeoTokyoStaker.sol

200: 	uint256 constant private _DIVISOR = 100;

203: 	uint256 constant private _BYTES_PER_POINT = 200 * 1e18;

946: 		uint256 vaultMultiplier = 100;

1022: 			citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;

1077: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

1098: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

1155: 			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

1331: 								uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;

1342: 								uint256 timeSinceReward = window.startTime - lastPoolRewardTime; // 1- 1000-500 // 2-2000-1000 //3-

1389: 				uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);

1623: 			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

```

### <a name="NC-4"></a>[NC-4] Imports should be cleaner
For readability, it is good practice to only import the libraries and contracts your need from a file.

Update your imports as such `import {contract1 , contract2} from "file.sol";`

```solidity
File: staking/BYTES2.sol

4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "../access/PermitControl.sol";

8: import "../interfaces/IByteContract.sol";

9: import "../interfaces/IStaker.sol";

```

```solidity
File: staking/NeoTokyoStaker.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

6: import "../access/PermitControl.sol";

7: import "../interfaces/IByteContract.sol";

8: import "../interfaces/IGenericGetter.sol";

```

