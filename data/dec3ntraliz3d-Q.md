## QA Report

## N-01
Incorrect constructor param description in `BYTES2.sol` constructor.

## Summary

In `BYTES2.sol` construction , `_bytes` param holds the address of the **BYTES 1.0 ERC-20** token contract address, however the current description says it is **BYTES** 2.0 contract address. 

In `BYTES2.sol` constructor, the `_bytes` parameter holds the address of the **BYTES 1.0 ERC-20**  token contract address, however the current description says it is the **BYTES 2.0** contract address.

[Link to the code on Github](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L70)
Here are the recommended changes.

```diff
 
-               @param _bytes The address of the BYTES 2.0 ERC-20 token contract.
+               @param _bytes The address of the BYTES 1.0 ERC-20 token contract.
```

## N-02
The `staking` function in `NeoTokyoStaker.sol` contract custom param description needs to be updated when **BYTES2** token is staked . 

## Summary

When `BYTES2` token is staked into S1 Citizen or S2 Citizen , 4th parameter is the S1 or S2 Citizen Ids. This needs to be included in the param description for clarity. 



[Link to the code on Github](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1189-L1190)

```solidity
function _stakeBytes (
		uint256
	) private {
		uint256 amount;
		uint256 citizenId;
		uint256 seasonId;
		assembly{
			amount := calldataload(0x44)
			citizenId := calldataload(0x64)   // This is the fourth parameter which is Citizen ID.
			seasonId := calldataload(0x84)
		}
		// Omited rest of the function 
}
```


Here are the recommended changes.

```diff
 
                @custom:param If the asset being staked is an S1 Citizen, this is the ID of 
-                       a Vault to attempt to optionally attach.
+                       a Vault to attempt to optionally attach. If the asset being staked is BYTES,
+                       this is the Citizen ID of the S1 or S2 Citizen that BYTES are being staked into.
```


## N-03

Incorrect Check for Valid Asset Type in `stake` function.

## Summary

In the `NeoTokyoStaker` contract's `stake` function, there is a check for valid asset type. However, the check is incorrect because there are only four valid asset types, and the enum value starts from 0. Here is the code on Github that needs modification:

[Link to the code on Github](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205-L1206)

Here are the recommended changes.

```diff
         // Validate that the asset being staked is of a valid type.
-        if (uint8(_assetType) > 4) {
+        if (uint8(_assetType) > 3) {
             revert InvalidAssetType(uint256(_assetType));
         }
```



## N-04

Incorrect Check for Valid Asset Type in `withdraw` function. 

## Summary

In the `NeoTokyoStaker` contract's `withdraw` function, there is a check for valid asset type. However, the check is incorrect because there are only four valid asset types, and the enum value starts from 0. Here is the code on Github that needs modification:

[Link to the code on Github](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668-L1669)

Here are the recommended changes.


```diff
-        if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
+        if (uint8(_assetType) == 2 || uint8(_assetType) > 3) {
             revert InvalidAssetType(uint256(_assetType));
         }


```