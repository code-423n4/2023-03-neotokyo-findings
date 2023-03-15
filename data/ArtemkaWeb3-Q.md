| Number | Issues details                             |
| ------ | -------------------------------------------|
| L-01   | No check for zero-address                  |

# [L-01] No check for zero-address
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114-L129
`address _to` can be zero. this can lead to unexpected behaviour!
