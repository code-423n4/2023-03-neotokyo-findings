# QA Report for NEO contest

## Overview

| ID  | Title | Instance Count |
| --- | --- | --- |
|[L-01] | Unsafe ABI encoding | 2   |
|[L-02] | Non valid type asset should be > 3 | 2 |
|[L-03] | Variable should not be declared multiple times | 4 |
|[L-04] | Event is emitted when zero amount | 1 |
|[NC-01] | Unnamed return parameters | 1   |
|[NC-02] | Code is unnecessary complex | 1   |
|[NC-03] | Use calldata instead of memory | 9   |



# [L-01] Unsafe ABI encoding

It is a common practice to use abi.encodeWithSelector to generate calldata for a low-level call. However, This function is not type-safe. because the returned values are error-prone and should be considered unsafe.

## Recommandation

Consider replacing all occurrences of unsafe ABI encodings with abi.encodeCall, which checks whether the supplied values actually match the types expected by the called function, and also avoids typographical errors.

Occurence in :
[NeoTokyoStaker.sol#L774](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L772-L780) 
[NeoTokyoStaker.sol#L804](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L801-L808) 

# \[L-02\] Non valid type asset should be greater than 3

In the NeoTokyoStaker.stake() and  NeoTokyoStaker.withdraw() functions there is a check to ensure that the specified asset is valid, but this check is not good. Since the number of valid assets is 4 (enum : from 0 to 3) the check must be like this :

[NeoTokyoStaker.sol#L1202](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1202) and [NeoTokyoStaker.sol#L1665](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1665)


```diff

@@ -1202,7 +1202,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        ) external nonReentrant {
 
                // Validate that the asset being staked is of a valid type.
-               if (uint8(_assetType) > 4) {
+               if (uint8(_assetType) > 3) {
                        revert InvalidAssetType(uint256(_assetType));
                }
@@ -1665,7 +1665,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        Validate that the asset being withdrawn is of a valid type. BYTES may not 
                        be withdrawn independently of the Citizen that they are staked into.
                */
-               if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
+               if (uint8(_assetType) == 2 || uint8(_assetType) > 3) {
                        revert InvalidAssetType(uint256(_assetType));
                }
 
```


# [L-03] Variable should not be declared multiple times

Declaring the same variable several times will create a new memory space each time. It is better to declare only once at the beginning of the code. Example : *timeSinceReward* variable in  NeoTokyoStaker.sol contract.

Occurence : 

[NeoTokyoStaker.sol#L1331](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1331)
[NeoTokyoStaker.sol#L1342](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1342)
[NeoTokyoStaker.sol#L1355](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1355)
[NeoTokyoStaker.sol#L1378](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1378)

Same for *currentRewardRate* variable in the getPoolReward function.

# [L-04] Event is emitted when zero amount

An event is emitted when zero amount is BYTES is changed to BYTES 2.0.

In [Bytes2.sol#L93-106](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/Bytes2.sol#L93-106)

```solidity
// Emit the upgrade event.
emit BytesUpgraded(msg.sender, _amount);
```

Consider reverting for zero amount.

# \[NC-01\] Unnamed return parameters

Consider adding and using named return parameters to improve explicitness and readability.

In [NeoTokyoStaker.sol#L1202](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1202)

## Recommandation

```diff
@@ -1408,7 +1408,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        */
        function claimReward (
                address _recipient
-       ) external returns (uint256, uint256) {
+       ) external returns (uint256 totalReward, uint256 totalTax) {
```

# [NC-02] code is unnecessary complex


[NeoTokyoStaker.sol#L1739-L1740](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1309-L1384)

I'm not used to see logic like that. Code is way complex and uses deprecating ways of programming like assinging value to the iterate parameter :

```solidity
// We have no forward goto and thus include this bastardry.
i = windowCount;
```


# Recommendation

 Write in simpler and shorter way in order to be capable to understand and to maintain the code easily. For example instead of using double iterating over windows time, I highly recommand you to create a logic with only one iteration (this will way more simple).
 

# NC-03  Use calldata instead of memory

Using calldata for array parameters is a well know gas optimization, but in addition using calldata can also oviates the need of loop iteration and saves runtime execution (especially for long array length). 

There is some place in the code where calldata must be used over memory : 

[NeoTokyoStaker.sol#L1739-L1740](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1739-L1740)
[NeoTokyoStaker.sol#L1761-1763](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1761-L1763)
[NeoTokyoStaker.sol#L1784-L1785](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1784-L1785)
[NeoTokyoStaker.sol#L1803-L1804](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1803-L1804)