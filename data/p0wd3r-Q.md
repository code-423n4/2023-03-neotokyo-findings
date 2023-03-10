# Use safeTransferFrom instead of transferFrom

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L191

```
/// The `transferFrom` selector for ERC-20 and ERC-721 tokens.
bytes4 constant private _TRANSFER_FROM_SELECTOR = 0x23b872dd;
```

Use `safeTransferFrom` instead of `transferFrom` to avoid locking NFTs in user contracts due to missing withdraw functionality.
Use `safeTransferFrom` to remind users to implement withdraw logic.

