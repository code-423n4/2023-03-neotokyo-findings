# [L-01] BYTES staking bonus points calculation contradicts the specification
## Targets
- https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1077
- https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1098
## Impact
Stakers of BYTES tokens will receive 100 times more points than expected.

Sine this issue doesn't negatively affect any parties (any user is able to choose to stake BYTES tokens and benefit from the increased rate), I think it's a low severity issue.
## Proof of Concept
The [NeoTokyoStaker._stakeBytes](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1044) function allows users to boost the reward of their staked S1 and S2 tokens by staking BYTES tokens. Staking of BYTES tokens increases the points of the staker, which increases the share of the rewards the stakers receive. As per the specification, the amount of points is calculated at the rate of 200 staked BYTES tokens per 1 point:
> Staking participants may also stake BYTES 2.0 tokens into their S1 or S2 Citizens in order to boost the points weight of those Citizens at a rate of 200 BYTES per point.

However, in the implementation the formula is different: for both [S1](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1077) and [S2 Citizen tokens](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1098), the amount of staked BYTES tokens is multiplied by 100 before being divided by the exchange rate:
> uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

As a result, stakers of BYTES tokens will receive 100 times more points than expected.
## Tools Used
Manual review
## Recommended Mitigation Steps
Consider updating the specification and mentioning the 100 multiplier. Or consider updating the code and removing the 100 multiplier.