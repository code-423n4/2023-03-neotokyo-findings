# Wrong check in the if clause in `stake` function 

### Details:
In the `stake` function at [line no 1205](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205) the if clause is checking that `_assetType` should be valid. But the `if (uint8(_assetType) > 4)` is wrong as in enum counting starts from `0` so the check should be `if (uint8(_assetType) > 3)` or `if (uint8(_assetType) >= 4)` for the `_assetType` to be valid.

### Vulnerable Code:
[NeoTokyoStaker.sol#L1205](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205)

### Impact:
If users specify 4 as `_assetType` then they will gonna spend `Gas` for nothing.