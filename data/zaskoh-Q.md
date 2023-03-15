## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| L-01 | Missing events on important state address updates | 3 |
| L-02 | Division before multiplication can lead to precision loss for points and shares | 3 |
| L-03 | Update open zeppelin contract dependency in package.json | 1 |
| L-04 | Consider using Ownable2Step | 2 |
| L-05 | Missing important input validation in functions | 11 |
| L-06 | Checking AssetType should be changed to reflect the amount of possible types | 2 |
| L-07 | Call lockLP in configureLP | 1 |
| L-08 | Pragma version ^0.8.19 version too recent to be trusted | 2 |
| L-09 | Unneccessary centralization risk on burn function in BYTES2 | 1 |

**Total: 26 instances over 9 issues**


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| NC-01 | Move owner checks to a modifier | 1 |
| NC-02 | Add indexed for amountOrTokenId in events | 2 |
| NC-03 | Use specific imports instead of just a global import | 9 |
| NC-04 | Consider a not so strict versioning for your main interfaces | 3 |
| NC-05 | Solidity pragma versioning | 2 |

**Total: 17 instances over 5 issues**


### Informational
| |Issue|Instances|
|-|:-|:-:|
| I-01 | Consider using AssetType.name instead of numbers for better identification | 1 |
| I-02 | Missing documentation for getPoolReward | 1 |

**Total: 2 instances over 2 issues**

---

## Low Risk Issues

### L-01 Missing events on important state address updates
Currently there are no events emitted for updates of important state variables. Events are used for off-chain analysis and can help protect and identify potential attacks.
You should consider emitting events after updating important state variables, especially when an address or contract is changed.

```solidity
File: contracts/staking/BYTES2.sol
162: 	function changeStakingContractAddress (
163: 		address _staker
164: 	) hasValidPermit(UNIVERSAL, ADMIN) external {
165: 		STAKER = _staker;
166: 	}

File: contracts/staking/BYTES2.sol
173: 	function changeTreasuryContractAddress (
174: 		address _treasury
175: 	) hasValidPermit(UNIVERSAL, ADMIN) external {
176: 		TREASURY = _treasury;
177: 	}

File: contracts/staking/NeoTokyoStaker.sol
1708: 	function configureLP (
1709: 		address _lp
1710: 	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
1711: 		if (lpLocked) {
1712: 			revert LockedConfigurationOfLP();
1713: 		}
1714: 		LP = _lp;
1715: 	}

```

### L-02 Division before multiplication can lead to precision loss for points and shares
When calculating points you use division before multiplication what can lead to a precision loss and less points and rewards for a user.

You should adapt the calculation to first muliply all values and in the end use the division.

```solidity
File: contracts/staking/NeoTokyoStaker.sol
1155: 			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

File: contracts/staking/NeoTokyoStaker.sol
1623: 			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
```

For the calculation for the user shares you should adapt the calculation to follow the muliply all values and divide in the end.

```solidity
File: contracts/staking/NeoTokyoStaker.sol
1388: 				uint256 share = points * _PRECISION / pool.totalPoints * totalReward; 
```

### L-03 Update open zeppelin contract dependency in package.json
You should update the Version dependency in your package.json file to >= 4.8.2, that it's not possible to install a Version before the current fixed version.
If an outdated OZ version is used (known vulnerabilities, see https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories) can lead to vulnerabilities.

```bash
File: package.json
9:     "@openzeppelin/contracts-upgradeable": "^4.4.2",

change to:

"@openzeppelin/contracts-upgradeable": ">=4.8.2",
```

### L-04 Consider using Ownable2Step
Currently both contracts implement the `PermitControl` that is using Ownable from open zeppelin. As both contracts depend hevely on a trusted and functioning owner you should consider using `Ownable2Step` instead of Ownable.

With `Ownable2Step` you remove danger of transfering the owner to a wrong address.

### L-05 Missing important input validation in functions
Even if the owner is trusted, to prevent potential misstakes and as a security messure you should consider implementing input validation checks, especially if it can break the contract if wrongly set values are set.

Currently the constructor of `BYTES2` and `NeoTokyoStaker` miss input validation checks for addresses. 
The contract `BYTES2` miss validation checks in the following functions `changeTreasuryContractAddress`, `changeStakingContractAddress`.
The contract `NeoTokyoStaker` in `configureLP`, `configureTimelockOptions`, `configureIdentityCreditYields`, `configureIdentityCreditPoints`, `configureVaultCreditMultipliers`, `configurePools` and `configureCaps`.

```solidity
File: contracts/staking/BYTES2.sol
81: 		BYTES1 = _bytes;
82: 		S1_CITIZEN = _s1Citizen;
83: 		STAKER = _staker;
84: 		TREASURY = _treasury;

File: contracts/staking/BYTES2.sol
165: 		STAKER = _staker;

File: contracts/staking/BYTES2.sol
165: 		STAKER = _staker;

File: contracts/staking/NeoTokyoStaker.sol
598: 		BYTES = _bytes;
599: 		S1_CITIZEN = _s1Citizen;
600: 		S2_CITIZEN = _s2Citizen;
601: 		LP = _lpToken;
602: 		IDENTITY = _identity;
603: 		VAULT = _vault;
604: 		VAULT_CAP = _vaultCap;
605: 		NO_VAULT_CAP = _noVaultCap;

File: contracts/staking/NeoTokyoStaker.sol
1714: 		LP = _lp;

File: contracts/staking/NeoTokyoStaker.sol
1743: 			timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];

File: contracts/staking/NeoTokyoStaker.sol
1770: 			] = _identityCreditYields[i];

File: contracts/staking/NeoTokyoStaker.sol
1788: 			identityCreditPoints[_identityCreditYields[i]] = _points[i]; 

File: contracts/staking/NeoTokyoStaker.sol
1807: 			vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];

File: contracts/staking/NeoTokyoStaker.sol
1830: 				_pools[_inputs[i].assetType].rewardWindows[j] =
1831: 					_inputs[i].rewardWindows[j];

File: contracts/staking/NeoTokyoStaker.sol
1855: 		VAULT_CAP = _vaultedCap;
1856: 		NO_VAULT_CAP = _unvaultedCap;

```

### L-06 Checking AssetType should be changed to reflect the amount of possible types
The `stake` and `withdraw` function in NeoTokyoStaker currently validate the input for `AssetType` that it's not bigger than 4. As the enum starts with 0 and only contains 4 values the max is 3. You should change the check to `uint8(_assetType) > 3` instead of > 4.

```solidity
File: contracts/staking/NeoTokyoStaker.sol
1205: 		if (uint8(_assetType) > 4) {

File: contracts/staking/NeoTokyoStaker.sol
1668: 		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
```

### L-07 Call lockLP in configureLP
You should call `lockLP` as soon as a LP is set via `configureLP` to prevent the described scenario in the documentation.

### L-08 Pragma version ^0.8.19 version too recent to be trusted
https://github.com/ethereum/solidity/blob/develop/Changelog.md 0.8.19 (2023-02-22) 0.8.17 (2022-09-08) 0.8.16 (2022-08-08) 0.8.15 (2022-06-15) 0.8.10 (2021-11-09)

Unexpected bugs can be reported in recent versions; Risks related to recent releases Risks of complex code generation changes Risks of new language features Risks of known bugs

Use a non-legacy and more battle-tested version

```solidity
File: contracts/staking/BYTES2.sol
2: pragma solidity ^0.8.19;

File: contracts/staking/NeoTokyoStaker.sol
2: pragma solidity ^0.8.19;
```

### L-09 Unneccessary centralization risk on burn function in BYTES2
The `burn` function in `BYTES2` can be used by an Admin / the BURN-Role to burn all tokens of any user without approvment from a user.

Consider updating the burn function to check if the request is coming from a future burn contract that will be responsible for burning of users and is approved from the user.

---

## Non-critical Issues

### NC-01 Move owner checks to a modifier
It's better to use a modifier for simple owner checks for an easier inspection of functions. In a function it can be forgotten and with a modifier it's easier to see how the function is protected.

```solidity
File: contracts/staking/NeoTokyoStaker.sol
1409: 	function claimReward (
1410: 		address _recipient
1411: 	) external returns (uint256, uint256) {
1412: 
1413: 		// This function may only be called by the BYTES contract.
1414: 		if (msg.sender != BYTES) {
1415: 			revert CallerIsNotBYTES();
1416: 		}
```

### NC-02 Add indexed for `amountOrTokenId` in events
You should consider adding indexed for `amountOrTokenId` in the events in NeoTokyoStaker.

```solidity
File: contracts/staking/NeoTokyoStaker.sol
539: 	event Stake (
540: 		address indexed staker,
541: 		address indexed asset,
542: 		uint256 timelockOption,
543: 		uint256 amountOrTokenId
544: 	);

File: contracts/staking/NeoTokyoStaker.sol
567: 	event Withdraw (
568: 		address indexed caller,
569: 		address indexed asset,
570: 		uint256 amountOrTokenId
571: 	);

```

### NC-03 Use specific imports instead of just a global import
For a better better developer experience it's better to use specific imports instead of just a global import. This helps to have a better overview what is realy needed and helps to have a clearer view of the contract.

```solidity
File: contracts/staking/NeoTokyoStaker.sol
4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
5: 
6: import "../access/PermitControl.sol";
7: import "../interfaces/IByteContract.sol";
8: import "../interfaces/IGenericGetter.sol";

File: contracts/staking/BYTES2.sol
4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
6: 
7: import "../access/PermitControl.sol";
8: import "../interfaces/IByteContract.sol";
9: import "../interfaces/IStaker.sol";
```

### NC-04 Consider a not so strict versioning for your main interfaces
At the moment your interfaces have a version for ^0.8.19. Consider a more open Version like ^0.8.0 so other projects can import your original interfaces and dont need to rewrite or change them if they don't use a version >= 0.8.19.

```solidity
File: contracts/interfaces/IByteContract.sol
2: pragma solidity ^0.8.19;

File: contracts/interfaces/IGenericGetter.sol
2: pragma solidity ^0.8.19;

File: contracts/interfaces/IStaker.sol
2: pragma solidity ^0.8.19;
```

### NC-05 Solidity pragma versioning
It's better to have a fixed version for your implementation contracts. Currently you use ^0.8.19 for your contracts but you should use the fixed Version 0.8.19 for them.

For your interfaces you could consider a ^0.8.0 version.

```solidity
File: contracts/staking/BYTES2.sol
2: pragma solidity ^0.8.19;

File: contracts/staking/NeoTokyoStaker.sol
2: pragma solidity ^0.8.19;
```

---

## Informational

### I-01 Consider using AssetType.name instead of numbers for better identification
Currently you check for uint8(_assetType) == 2 instead of AssetType.BYTES. For better reading you should consider changing to use the specific enum name for comparison.

```solidity
File: contracts/staking/NeoTokyoStaker.sol
1668: 		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
```

### I-02 Missing documentation for getPoolReward
The function getPoolReward in NeoTokyoStaker is missing the @return description in the documentatio.

```solidity
File: contracts/staking/NeoTokyoStaker.sol
1264: 	function getPoolReward (
1265: 		AssetType _assetType,
1266: 		address _recipient
1267: 	) public view returns (uint256, uint256) {
```
