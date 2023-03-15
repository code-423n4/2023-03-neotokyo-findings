# Neo Tokyo contest QA report

## Summary

The following QA issues were found during the code audit:

- [QA-1] Typo in `BYTES2.getReward()` variable name (1 instance)
- [QA-2] Natspec of `BYTES2.getReward()` is misleading (1 instance)
- [QA-3] Floating pragma (2 instances)

Total 3 instances of 4 issues.

## [QA-1] Typo in `BYTES2.getReward()` variable name

This variable name is a typo:

```solidity
uint256 daoCommision
```

should be:

```solidity
uint256 daoCommission
```

Fixing this typo will improve readability of the code.

## [QA-2] Natspec of `BYTES2.getReward()` is misleading

The Natspec of `BYTES2.getReward()` says:

```solidity
	/**
		This function is called by the S1 Citizen contract to emit BYTES to callers 
		based on their state from the staker contract.

		@param _to The reward address to mint BYTES to.
	*/
```

It says "this function is called by the S1 Citizen contract", which indicates the function should be internal instead of external.

However, in the test `NeoTokyoStaker.test.js`, there are many cases where the users Alice and Bob are calling `BYTES2.getReward()`. For example, at line 431 and 432:

```solidity
// Have Bob claim his S1 Citizen's BYTES reward.
await NTCitizenDeploy.connect(bob.signer).getReward();
```

Based on my understanding, the Natspec of `BYTES2.getReward()` should be fixed.

## [QA-3] Floating pragma

Floating pragma is used for both contracts:

```solidity
pragma solidity ^0.8.19;
```

In general, fixed pragma is recommended to avoid unexpected bugs:

```solidity
pragma solidity 0.8.19;
```
