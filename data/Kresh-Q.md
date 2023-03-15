### Out of gas because of `getPoolReward`
#### Description
Because of `getPoolReward` function's strcture, it is possible for `stake` to get an "out of gas" error (for every new stake by user it needs about 13k more gas). `withdraw` and `claimReward` functions spend less gas, so the full lock of the NFT is impossible (only partial because of high gas fee).

#### Recomendation
That is architecture's disadvantage

### Valid useless parameter
#### Description
For `stake` there is an invalid check for assetType, it accepts 4, however it will revert later on dynamic function calling (line https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205) 
For `withdraw` is also accepts 4, and still will revert later (line https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668)

#### Recomendation
Replace `> 4` with `> 3`

### Improper variable name
#### Description
`getCreditYield` function has an improper variable name `_vaultId` (line https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L627)

#### Recomendation
Replace it with `_citizenVaultId`

