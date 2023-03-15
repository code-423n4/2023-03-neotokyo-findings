# L-01: ****Use interface type instead of the address for type safety****

Impact:

When a function takes a contract address as an argument, it is better to pass an interface or contract type rather than raw `address`. If the function is called elsewhere within the source code, the compiler it will provide additional type safety guarantees.

Examples:
[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L43](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L43)

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L226](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L226)

Recommendation
For both `BYTES2.sol` and `NeoTokyoStaker.sol` to initialize external contracts such as `BYTES1` `S1_CITIZEN` , `S2_CITIZEN` in `constructor` with their respective interface types instead of raw addresses

Links:

[https://consensys.net/blog/developers/solidity-best-practices-for-smart-contract-security/](https://consensys.net/blog/developers/solidity-best-practices-for-smart-contract-security/)

# L-02: Sanitize input addresses

Impact:

The presence of zero-value addresses, especially in `constructor` implementations can cause the contract to be permanently inoperable. 

Examples:

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75)

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75)

Recommendation

In both `BYTES2.sol` and `NeoTokyoStaker.sol` , some basic sanitization should be put in place by ensuring that each `address` specified is non-zero.

# L-03: Use proper execution flows

Impact:

In `NeoTokyoStaker.sol` the functions `_withdrawS1Citizen(), _withdrawS2Citizen() and _withdrawLP()` do not implement correct execution flows and thus contain incorrect system states when they perform outward transfer of assets 

For example, in [https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1459], the S1 Citizen is transferred out (L1483) before contract state is updated in L1517-1521.

While the functions are protected against re-entrancy attacks, it is possible that external contracts that rely on `NeoTokyoStaker.sol` data may be prone to cross-contract re-entrancy as a result of incorrect system state during these withdraws


Examples
[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1459](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1459)

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1534](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1534)

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1597](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1597)

Recommendations:

We advise that the above withdraw functions move the `_assetTransferFrom` and `_assetTransfer` call to the end of the function. This recommendation abides by the checks-effects-integration pattern.

# L-04: Allow access control till correct timestamp

Impact
In `PermitControl.sol` , function `hasRight` returns `false` if the permission `timestamp` is exactly equal to `block.timestamp`. So for example, if a user was granted permission until timestamp 123456, the function would return false (i.e. no permission) if block.timestamp was 123456.

Links: 

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/access/PermitControl.sol#L195](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/access/PermitControl.sol#L195)

Recommendation:

It is not clear if this is the intended design of access control. However, going by the `hasRightUntil` read function, it is implied that permission should be granted up till and including the `timestamp` declared. As such, the `hasRight` function should return `true` if `permissions[_address][_circumstance][_right] >= block.timestamp;`