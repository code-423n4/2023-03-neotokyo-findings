Neo Tokyo GAS Report5688638
[G-1] Using `storage` instead of `memory` saves gas

```
File: contracts/staking/NeoTokyoStaker.sol
719  StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
736  StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
```

