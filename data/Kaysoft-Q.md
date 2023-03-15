## [L-01] AVOID FLOATING PRAGMA
Locking the Solidity pragma version ensures that a contract is not accidentally deployed with an outdated or different compiler version from which the contract has been tested
see: https://swcregistry.io/docs/SWC-103
Files: All files.

#### Recommended Mitigation Steps
Consider locking the pragma version by changing `pragma solidity ^0.8.19;` to `pragma solidity 0.8.19;`


## [L-02] USE NAMED IMPORTS
Use specific named imports for example 
`import {ReenterancyGuard}  from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
see: https://betterprogramming.pub/solidity-tutorial-all-about-imports-c65110e41f3a



## [L-03] USE LATEST STABLE VERSION OF @openzeppelin/contracts
The latest version of @openzeppelin/contracts at the moment is 4.8.2 and the project used version 4.3.1 which is outdated
File: package.json
#### Recommended Mitigation Steps
Consider using the latest stable version of @openzeppelin/contracts as there will have been security bug fixes in the latest version.

## [L-04] SOLIDITY COMPILER OPTIMIZATION CAN BE PROBLEMATIC
File: hardhat.config.js
The project has enabled compiler optimization. 
```
   module.exports = {
	solidity: {
	   compilers: [
			{
				version: '0.8.11',
				settings: {
					optimizer: {
						enabled: true,
						runs: 200, 
						details: {
```
There have been several bugs reported regarding the compiler optimization. 
see: https://blog.soliditylang.org/2017/05/03/solidity-optimizer-bug/

## [NC-1] EMIT EVENT FOR USEFUL STATE UPDATES

Functions `configureLP` and `lockLP` made useful state changes but do not emit event. Emitted events can be monitored externally to know when those state change. Consider emitting event in both functions.
```
     function configureLP(address _lp) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
        if (lpLocked) {
            revert LockedConfigurationOfLP();
        }
        LP = _lp;
    }
```

```
   function lockLP() external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
        lpLocked = true;
    }

```
```
function configureCaps(uint256 _vaultedCap, uint256 _unvaultedCap)
        external
        hasValidPermit(UNIVERSAL, CONFIGURE_CAPS)
    {
        VAULT_CAP = _vaultedCap;
        NO_VAULT_CAP = _unvaultedCap;
    }
```