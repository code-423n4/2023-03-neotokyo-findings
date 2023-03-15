# **G-01: Use storage instead of memory struct locally**

In function `getPoolReward`, structs: `s1Citizen`, `s2Citizen` and `window` are declared in `memory` but only used for read.

We recommend `storage` for these structs which would save at least 9k gas (could be much higher as the structs are used in loops)

Links:

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1281](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1281)

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1290](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1290)

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1313](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1313)

# G-02: Use external instead of public

`PermitControl.sol` is inherited in both `BYTES2.sol` and `NeoTokyoStaker.sol`.

Function `setPermit` is declared `public` but is not called inside any contract. It should be set to `external` which would save approximately 200 gas

Links:
[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/access/PermitControl.sol#L144](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/access/PermitControl.sol#L144)