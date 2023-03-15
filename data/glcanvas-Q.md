# [L-1] Possible to mint zero amount

In BYTES2 contract in function `upgradeBytes` it's possible to mint zero amount of tokens.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L93-L106
This might be uncomfortable and misleading.

# [L-2] Typos

Please, fix typos:
* https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L119 daoCommision => daoCommission.
* https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1776 funciton => function.

# [L-3] Use constant instead of number in code

This improves readability, replace 100 to ONE_HUNDERD_PERCENT or any meaningful name

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L1022
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L1098
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L1155
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L1389
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L1623

# [L-4] Redundant division
Please rewrite https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L1022
to just:
`citizenStatus.points = timelockMultiplier;` 
This statements are similar.

And rewrite https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L1029
to: 
`pool.totalPoints += timelockMultiplier`


# [Q-1] Why two divisions 
In lines:
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L968-L970

Why use two divisions, looks like only one needed for `vaultMultiplier`.
And correct expression is: 
```solidity
citizenStatus.points = identityPoints * vaultMultiplier * timelockMultiplier / _DIVISOR;
```

