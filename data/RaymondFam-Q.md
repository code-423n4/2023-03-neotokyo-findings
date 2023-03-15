## Improper use of `<<`
I have separately submitted a vulnerability report on `>>`, but since `<<` in this instance concerns only an emitted event, I am including this issue in this QA report.

[File: NeoTokyoStaker.sol#L1110-L1115](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1110-L1115)

```solidity
		emit Stake(
			msg.sender,
			BYTES,
			(seasonId << 128) + citizenId,
			amount
		);
```
As can be seen from the code block above, `seasonId` which is either [`1`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1060) or [`2`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1084) is multiplied by 2 ** 128.

The implication on off chain logging is going to incur great confusion and difficulty in data interpretation as the third parameter of the emitted output entails one of the two very big numbers plus `citizenId`.

If `seasonId == 1`, it will be `340282366920938463463374607431768211456 + citizenId`.

If `seasonId == 2`, it will be `680564733841876926926749214863536422912 + citizenId`.  

## Typo mistakes
[File: NeoTokyoStaker.sol#L619](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L619)

```diff
-	if there is one. This parameter is separated to optimized for callers who
+	if there is one. This parameter is separated to optimize for callers who
```
[File: NeoTokyoStaker.sol#L1373](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1373)

```diff
-	Otherwise, the last reward rate, and therefore the entireity of 
+	Otherwise, the last reward rate, and therefore the entirety of 
```
[File: NeoTokyoStaker.sol#L417](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L417)

```diff
-	@param timelockEndTime The tiume at which the forced, timelocked staking of
+	@param timelockEndTime The time at which the forced, timelocked staking of
```
## Comment and code mismatch
In BYTES2.sol, the comment for `_bytes` (that is supposedly assigned to `BYTES1`) mistakenly relates to the BYTES 2.0 `(instead of 1.0)` ERC-20 token contract. Consider having the comment corrected as follows:

[File: BYTES2.sol#L70](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L70)

```diff
-	@param _bytes The address of the BYTES 2.0 ERC-20 token contract.
+	@param _bytes The address of the BYTES 1.0 ERC-20 token contract.
```
Similarly, the comment for the custom param of `withdraw()` in NeoTokyoStaker.sol relates to the third (instead of the second) parameter:

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1653

```diff
-	@custom:param The third parameter is overloaded to have different meaning
+	@custom:param The second parameter is overloaded to have different meaning
``` 

## Gas griefing/theft is possible on unsafe external call
`return` data (bool success,) has to be stored due to EVM architecture, if in a usage like below, ‘out’ and ‘outsize’ values are given (0,0). Thus, this storage disappears and may come from external contracts a possible gas grieving/theft problem is avoided as denoted in the link below:

https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19

Here are the two instances entailed:

[File: NeoTokyoStaker.sol#L772-L780](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L772-L780)

```solidity
            (bool success, bytes memory data) = 
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_FROM_SELECTOR,
					_from,
					_to, 
					_idOrAmount
				)
			);
```
[File: NeoTokyoStaker.sol#L801-L808](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L801-L808)

```solidity
        (bool success, bytes memory data) = 
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_SELECTOR,
					_to, 
					_amount
				)
			);
```
## Missing input parameter variable
`_timelock` should be included in the input parameter of `_stakeBytes()` just like its three other counterpart functions:

[File: NeoTokyoStaker.sol#L1044-L1046](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1044-L1046)

```diff
	function _stakeBytes (
-		uint256
+		uint256 _timelock
	) private {
```