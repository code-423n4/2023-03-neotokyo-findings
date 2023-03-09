https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668
Both revert if `uint8(_assetType) > 4`.

Meanwhile both functions only handle 0, 1, 2, 3 as value of `uint8(_assetType)`.
See:
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1237
- https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1683)

Recommendation:
Replace `uint8(_assetType) > 4` with `uint8(_assetType) > 3`.