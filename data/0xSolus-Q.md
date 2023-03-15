**---- Bytes.sol ----**

`Constructor` not checking for 0 addresses when deployed in `Bytes.sol` [L80](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L80). 
A mistake on the address is going to affect the whole implementation of the contract
and can affect the tokens being minted and burned to 0 address.  

`UpgradeBytes(_amounts)` avoids 0 amount check in `Bytes.sol` [L95](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L95), 
if a user by accident calls the function with 0 as `_amount` it will actually execute costing gas to the user. 
The recommendation is to make sure that `require(_amount != 0)`. 
The following assertion in the tests doesn't revert:  
```
it("should revert on amount 0", async function () {
   await expect(NTBytes2_0.connect(alice.signer).upgradeBytes(0)).to.be.reverted
   })
```

**---- NeoTokyoStaker.sol ----**

In [L1205](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205) consider omitting 
the check of the range of enum `_assetType` as solidity already checks for it and emits a Panic error [check](https://docs.soliditylang.org/en/v0.8.19/types.html#enums). 
In this case the `revert InvalidAssetType(uint256(_assetType))` is never hit.  
In any case, the range of `enum AssetType` is 0..3, so the proper range check will be: 
```
if (uint8(_assetType) > 3) {
  revert InvalidAssetType(uint256(_assetType));
}  
```
There's a similar occurrence in [L1688](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668)

