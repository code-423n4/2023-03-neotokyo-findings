**L01. Missing zero validations in constructors**
Both `BYTES2` and `NeoTokyoStaker` constructors set up immutable variables without zero address checks. In case of mistakes deploy with default values there would be no possibility to update addresses for `BYTES1`, `S1_CITIZEN`, `S2_CITIZEN`, `IDENTITY`, and `VAULT`. Which could result in the need for contract redeployment and pay extra Gas.

**Recommendation:** Add zero checks during contract initialization

**L02. Solidity style guide violation.**
According to the Solidity style guide, - upper-case variable naming should be applied only for constants, but now mutable state variables are named in the same style.

**Recommendation:** Renamed `STAKER`, `TREASURY`, `VAULT_CAP`, `NO_VAULT_CAP`, `LP` to use lower case

**L03. Missing event editing during critical changes in contract**
Events should be emitted during changes in the contract to simplify the off-chain analysis of contract behavior. 

**Recommendation:** Add event tracking in `changeStakingContractAddress`, `changeTreasuryContractAddress`, `configureCaps`, `configurePools`, `configureVaultCreditMultipliers`, `configureIdentityCreditPoints`, `configureIdentityCreditYields`, `configureTimelockOptions`, `lockLP`, `configureLP` functions.

**L04. Typos**
Fix typos in contract NatSpecs:
«ctiizen» -> «citiizen» in event `Stake` NatSpec
«configued» -> «configured» in `getConfiguredVaultMultiplier` NatSpec
«entireity» -> «entirety» in Line 1373 of `NeoTokyoStaker` contract
«funciton» -> «function» in `configureIdentityCreditPoints` NatSpec
