
# Low Risk and Non-Critical Issues

# [L 01] Avoid using latest version of Solidity due to unknown bugs (02 Instances)

Precaution should be taken in using solidity latest released version that can impact the project based on reason of unknown bugs.

Link to the code:
1.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2
2.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2


# [L-02] Minting tokens to the zero address should be avoided (01 Instances)

Address(0) check is missing in function, consider applying check to ensure tokens or tickets aren’t minted to the zero address.

Link to the code:
1.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L102


# [N-01] Constructor lacks address(0) check (02 Instances)

Zero-address check should be used in the constructors, to avoid the risk of setting a storage variable as address(0) at deploying time.

	


Link to the code:

1.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75
2.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588

# [N-02] Floating of Pragma (02 Instances)
Locking pragma version ensures contracts are not being deployed on an outdated compiler version.

Link to the code:

1.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2
2.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2


# [N-03] Missing emit at state change (04 Instances)
Functions in which important state change takes place should emit relevant changes.

Link to the code:
1.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114
2.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L140
3.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162
4.	https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173
