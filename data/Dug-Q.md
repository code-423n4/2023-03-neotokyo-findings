# Custom error not returned on invalid asset type

In both the `stake` and `withdraw` functions of `NeoTokyoStaker.sol`, the following check exists...

```solidity
      if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
          revert InvalidAssetType(uint256(_assetType));
      }
``` 

This implies that a `uint8(_assetType)` value of `4` is valid. However the `AssetType` struct is defined as follows...

```solidity
    enum AssetType {
        S1_CITIZEN,
        S2_CITIZEN,
        BYTES,
        LP
    }
```

This results in the `InvalidAssetType(uint256 assetType)` error not being returned in all cases where an invalid asset type is provided.

# State changes should emit events

In `BYTES2.sol`, the `changeStakingContractAddress` and `changeTreasuryContractAddress` functions update storage variables without emitting events.

# Staker contract address should be immutable/lockable

Considering the heavy interdependence between the `NeoTokyoStaker.sol` and `BYTES2.sol` contracts, it would be prudent to make the `STAKER` storage variable immutable or lockable.