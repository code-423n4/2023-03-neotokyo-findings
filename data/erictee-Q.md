### [N-01] Missing checks for `address(0)` when assigning values to address state variables	


#### Impact
Zero-address checks are a best practice for input validation of critical address parameters. Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

#### Findings:
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L85
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L606


### [N-02] Avoid floating pragmas for non-library contracts.

#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
staking/NeoTokyoStaker.sol:L2     pragma solidity ^0.8.19;

staking/BYTES2.sol:L2     pragma solidity ^0.8.19;

```

