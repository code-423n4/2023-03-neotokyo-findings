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
## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Consider fully equipping all contracts with complete set of NatSpec to better facilitate users/developers interacting with the protocol's smart contracts.

For example, the following function instance has missing `@return`:

[File: NeoTokyoStaker.sol#L1257-L1267](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1257-L1267)

```solidity
	/**
		This function supports retrieving the reward and tax earned by a particular 
		`_recipient` on a specific pool of type `_assetType`.
		@param _assetType The type of the asset to calculate rewards for.
		@param _recipient The recipient of the reward.
	*/
	function getPoolReward (
		AssetType _assetType,
		address _recipient
	) public view returns (uint256, uint256) {
```
## Use `delete` to clear variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address or a boolean to false. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic.

For instance, the `a[x] = 0` and `b[x] = false` instances below may be refactored as follows:

[File: NeoTokyoStaker.sol#L1517-L1521](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517-L1521)

```diff
-		stakedCitizen.stakedBytes = 0;
+		delete stakedCitizen.stakedBytes;
-		stakedCitizen.timelockEndTime = 0;
+		delete stakedCitizen.timelockEndTime;
-		stakedCitizen.points = 0;
+		delete stakedCitizen.points;
-		stakedCitizen.hasVault = false;
+		delete stakedCitizen.hasVault;
-		stakedCitizen.stakedVaultId = 0;
+		delete stakedCitizen.stakedVaultId;
```
## Tokens accidentally sent to the contract cannot be recovered
It is deemed unrecoverable if the tokens accidentally arrive at the contract addresses, which has happened to many popular projects. Consider adding a recovery code to your critical contracts.

## Project Upgrade and Stop Scenario
At the start of the project, the system may need to be stopped or upgraded. Consider having a script beforehand and add it to the documentation. This can also be called an ” EMERGENCY STOP (CIRCUIT BREAKER) PATTERN “.

## Non-compliant contract layout with Solidity's Style Guide
According to Solidity's Style Guide below:

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

In order to help readers identify which functions they can call, and find the constructor and fallback definitions more easily, functions should be grouped according to their visibility and ordered in the following manner:

constructor, receive function (if exists), fallback function (if exists), external, public, internal, private

And, within a grouping, place the `view` and `pure` functions last.

Additionally, inside each contract, library or interface, use the following order:

type declarations, state variables, events, modifiers, functions

Consider adhering to the above guidelines for all contract instances entailed.

## Lack of events for critical operations
Critical operations not triggering events will make it difficult to review the correct behavior of the deployed contracts. Users and blockchain monitoring systems will not be able to detect suspicious behaviors at ease without events. 

Consider adding events where appropriate for all critical operations for better support of off-chain logging API, specifically on the administrative configurable functions.