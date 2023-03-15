### Table of contents
- [FINDINGS](#findings)
  - [Note on Gas estimates.](#note-on-gas-estimates)
- [Tighly pack storage variables/optimize the order of variable declaration(2k gas saved in total)](#tighly-pack-storage-variablesoptimize-the-order-of-variable-declaration2k-gas-saved-in-total)
  - [Pack address public LP with bool public lpLocked(Saves 1 SLOT: 2k Gas)](#pack-address-public-lp-with-bool-public-lplockedsaves-1-slot-2k-gas)
- [Use calldata instead of memory for function parameters](#use-calldata-instead-of-memory-for-function-parameters)
  - [NeoTokyoStaker.sol.configureTimelockOptions(): Use calldata on \_timelockIds  and \_encodedSettings(Saves 1191gas on average)](#neotokyostakersolconfiguretimelockoptions-use-calldata-on-_timelockids--and-_encodedsettingssaves-1191gas-on-average)
  - [NeoTokyoStaker.sol.configureIdentityCreditYields(): Use calldata on \_citizenRewardRates ,\_vaultRewardRates and \_identityCreditYields(Saves 19784 gas on average)](#neotokyostakersolconfigureidentitycredityields-use-calldata-on-_citizenrewardrates-_vaultrewardrates-and-_identitycredityieldssaves-19784-gas-on-average)
  - [NeoTokyoStaker.sol.configureIdentityCreditPoints(): Use calldata on \_identityCreditYields and \_points(Saves 2065 gas on average)](#neotokyostakersolconfigureidentitycreditpoints-use-calldata-on-_identitycredityields-and-_pointssaves-2065-gas-on-average)
  - [NeoTokyoStaker.sol.configureVaultCreditMultipliers(): Use calldata on \_vaultCreditMultipliers and \_multipliers(Saves 3696 gas on average)](#neotokyostakersolconfigurevaultcreditmultipliers-use-calldata-on-_vaultcreditmultipliers-and-_multiplierssaves-3696-gas-on-average)
- [IF's/require() statements that check input arguments should be at the top of the function](#ifsrequire-statements-that-check-input-arguments-should-be-at-the-top-of-the-function)
  - [Refactor the IF statement here to avoid wasting gas reading from storage](#refactor-the-if-statement-here-to-avoid-wasting-gas-reading-from-storage)
- [No need to cache as the cached value is being referenced only once](#no-need-to-cache-as-the-cached-value-is-being-referenced-only-once)
  - [NeoTokyoStaker.sol.getCreditYield(): IGenericGetter(S1\_CITIZEN) and IGenericGetter(VAULT) should not be cached](#neotokyostakersolgetcredityield-igenericgetters1_citizen-and-igenericgettervault-should-not-be-cached)
  - [NeoTokyoStaker.sol.getConfiguredVaultMultiplier(): IGenericGetter(VAULT) should not be cached](#neotokyostakersolgetconfiguredvaultmultiplier-igenericgettervault-should-not-be-cached)
- [Multiple accesses of a mapping/array should use a local variable cache](#multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache)
  - [NeoTokyoStaker.sol.\_stakeLP(): stakerLPPosition\[msg.sender\] should be cached in local storage](#neotokyostakersol_stakelp-stakerlppositionmsgsender-should-be-cached-in-local-storage)
  - [NeoTokyoStaker.sol.claimReward(): lastRewardTime\[\_recipient\] should be cached in local storage](#neotokyostakersolclaimreward-lastrewardtime_recipient-should-be-cached-in-local-storage)
- [Not in scope](#not-in-scope)
- [Pack structs by putting data types in ascending size](#pack-structs-by-putting-data-types-in-ascending-size)
  - [NeoTokyoStaker.sol.StakedS1Citizen{}: Pack timelockEndTime with hasVault.(Saves 1 SLOT: 2k Gas)](#neotokyostakersolstakeds1citizen-pack-timelockendtime-with-hasvaultsaves-1-slot-2k-gas)
  - [NeoTokyoStaker.sol.StakedS1CitizenOutput{}: Pack timelockEndTime with hasVault (Save 1 SLOT: 2k Gas)](#neotokyostakersolstakeds1citizenoutput-pack-timelockendtime-with-hasvault-save-1-slot-2k-gas)

## FINDINGS
NB: Some functions have been truncated where necessary to just show affected parts of the code
Through out the report some places might be denoted with audit tags to show the actual place affected.

### Note on Gas estimates.
I've tried to give the exact amount of gas being saved from running the included tests. Whenever the function is within the test coverage, the average gas before and after will be included, and often a diff of the code will also accompany this.

Some functions are not covered by the test cases or are internal/private functions. In this case, the gas can be estimated by looking at the opcodes involved. 

**Special attention should be paid on the `use calldata instead of memory` as the average gas saved from the tests included seems to be too much**

## Tighly pack storage variables/optimize the order of variable declaration(2k gas saved in total)

Here, the storage variables can be tightly packed 

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L232-L511
### Pack address public LP with bool public lpLocked(Saves 1 SLOT: 2k Gas)
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
232:	address public LP;

249:	uint256 public VAULT_CAP;
	
510:	/// Whether or not setting the LP token contract address is locked.
511:	bool public lpLocked;
```

```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..09710a4 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -231,6 +231,9 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        /// The address of the LP token contract.
        address public LP;

+       /// Whether or not setting the LP token contract address is locked.
+       bool public lpLocked;


-       /// Whether or not setting the LP token contract address is locked.
-       bool public lpLocked;
```




## Use calldata instead of memory for function parameters
**Some gas saved seems too much to be true, but included as it is from running the included tests**
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.


https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1737-L1746
### NeoTokyoStaker.sol.configureTimelockOptions(): Use calldata on \_timelockIds  and \_encodedSettings(Saves 1191gas on average)
**Gas benchmarks**
|        | Min    | Max   | Avg   |
| ------ | --- | ------- | ----- |
| Before |  139971      |162925 | 145710 |
| After  |  138817   | 161625 | 144519 |
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
1737:	function configureTimelockOptions (
1738:		AssetType _assetType,
1739:		uint256[] memory _timelockIds,
1740:		uint256[] memory _encodedSettings
1741:	) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
1742:		for (uint256 i; i < _timelockIds.length; ) {
1743:			timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];
1744:			unchecked { ++i; }
1745:		}
1746:	}
```

```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..485c14f 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1736,8 +1736,8 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        */
        function configureTimelockOptions (
                AssetType _assetType,
-               uint256[] memory _timelockIds,
-               uint256[] memory _encodedSettings
+               uint256[] calldata _timelockIds,
+               uint256[] calldata _encodedSettings
        ) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1760-L1773
### NeoTokyoStaker.sol.configureIdentityCreditYields(): Use calldata on \_citizenRewardRates ,\_vaultRewardRates and \_identityCreditYields(Saves 19784 gas on average)
**Gas benchmarks**
|        | Min    | Max   | Avg   |
| ------ | --- | ------- | ----- |
| Before |  -      |-  | 581101 |
| After  |  -   | - | 561317 |

```solidity
File: /contracts/staking/NeoTokyoStaker.sol
1760:	function configureIdentityCreditYields (
1761:		uint256[] memory _citizenRewardRates, 
1762:		string[] memory _vaultRewardRates,
1763:		string[] memory _identityCreditYields
1764:	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1765:		for (uint256 i; i < _citizenRewardRates.length; ) {
1766:			identityCreditYield[
1767:				_citizenRewardRates[i]
1768:			][
1769:				_vaultRewardRates[i]
1770:			] = _identityCreditYields[i];
1771:			unchecked { ++i; }
1772:		}
1773:	}
```

```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..97fff89 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1758,9 +1758,9 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        `_vaultRewardRates`.
        */
        function configureIdentityCreditYields (
-               uint256[] memory _citizenRewardRates,
-               string[] memory _vaultRewardRates,
-               string[] memory _identityCreditYields
+               uint256[] calldata _citizenRewardRates,
+               string[] calldata _vaultRewardRates,
+               string[] calldata _identityCreditYields
        ) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1783-L1791
### NeoTokyoStaker.sol.configureIdentityCreditPoints(): Use calldata on \_identityCreditYields and \_points(Saves 2065 gas on average)
**Gas benchmarks**
|        | Min    | Max   | Avg   |
| ------ | --- | ------- | ----- |
| Before |  -      |-  | 96450 |
| After  |  -   | - | 94385 |
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
1783:	function configureIdentityCreditPoints (
1784:		string[] memory _identityCreditYields,
1785:		uint256[] memory _points
1786:	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1787:		for (uint256 i; i < _identityCreditYields.length; ) {
1788:			identityCreditPoints[_identityCreditYields[i]] = _points[i];
1789:			unchecked { ++i; }
1790:		}
1791:	}
```

```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..98b9f86 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1781,8 +1781,8 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        `_identityCreditYields`.
        */
        function configureIdentityCreditPoints (
-               string[] memory _identityCreditYields,
-               uint256[] memory _points
+               string[] calldata _identityCreditYields,
+               uint256[] calldata _points
        ) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1802-L1810
### NeoTokyoStaker.sol.configureVaultCreditMultipliers(): Use calldata on \_vaultCreditMultipliers and \_multipliers(Saves 3696 gas on average)
**Gas benchmarks**
|        | Min    | Max   | Avg   |
| ------ | --- | ------- | ----- |
| Before |  -      |-  | 167875 |
| After  |  -   | - | 164179 |

```solidity
File: /contracts/staking/NeoTokyoStaker.sol
1802:	function configureVaultCreditMultipliers (
1803:		string[] memory _vaultCreditMultipliers,
1804:		uint256[] memory _multipliers
1805:	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1806:		for (uint256 i; i < _vaultCreditMultipliers.length; ) {
1807:			vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];
1808:			unchecked { ++i; }
1809:		}
1810:	}
```

```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..8dc58e9 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1800,8 +1800,8 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        value in `_vaultCreditMultipliers`.
        */
        function configureVaultCreditMultipliers (
-               string[] memory _vaultCreditMultipliers,
-               uint256[] memory _multipliers
+               string[] calldata _vaultCreditMultipliers,
+               uint256[] calldata _multipliers
        ) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
                for (uint256 i; i < _vaultCreditMultipliers.length; ) {
                        vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];
```


## IF's/require() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.


### Refactor the IF statement here to avoid wasting gas reading from storage
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L875-L987
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
875:	function _stakeS1Citizen (
876:		uint256 _timelock
877:	) private {

899:		// Retrieve storage for tracking the staking state of this S1 Citizen.
900:		StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];

902:		// Attach a getter to the S1 Citizen and check for a component Vault.
903:		IGenericGetter citizen = IGenericGetter(S1_CITIZEN);
904:		uint256 citizenVaultId = citizen.getVaultIdOfTokenId(citizenId);

910:		if (citizenVaultId != 0 && vaultId != 0) {
911:			revert CitizenAlreadyHasVault(citizenVaultId, vaultId);

917:		} else if (citizenVaultId != 0 && vaultId == 0) {
918:			citizenStatus.hasVault = true;
919:			vaultId = citizenVaultId;

926:		} else if (citizenVaultId == 0 && vaultId != 0) {
927:			_assetTransferFrom(VAULT, msg.sender, address(this), vaultId);
928:			citizenStatus.hasVault = true;
929:			citizenStatus.stakedVaultId = vaultId;
930:		}
```

On line 900 we read from storage , on line 911, we have a revert statement if `citizenVaultId != 0 && vaultId != 0` If we end up reverting, we waste the gas used on reading from storage. We can refactor the code, to fail early and cheaply ie not wasting gas reading from storage.
As the revert is part of an `if... else if` we can refactor that to use solely `if` 

```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..a791c2d 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -896,9 +896,6 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                */
                _assetTransferFrom(S1_CITIZEN, msg.sender, address(this), citizenId);

-               // Retrieve storage for tracking the staking state of this S1 Citizen.
-               StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
-
                // Attach a getter to the S1 Citizen and check for a component Vault.
                IGenericGetter citizen = IGenericGetter(S1_CITIZEN);
                uint256 citizenVaultId = citizen.getVaultIdOfTokenId(citizenId);
@@ -914,7 +911,11 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        If no optional vault is provided, and the S1 Citizen being staked already
                        has an existing Vault, override the provided `vaultId`.
                */
-               } else if (citizenVaultId != 0 && vaultId == 0) {
+               }
+               // Retrieve storage for tracking the staking state of this S1 Citizen.
+               StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
+
+               if (citizenVaultId != 0 && vaultId == 0) {
                        citizenStatus.hasVault = true;
                        vaultId = citizenVaultId;

@@ -923,7 +924,8 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        Vault is staked and the S1 Citizen is recorded as carrying an optional,
                        separately-attached vault.
                */
-               } else if (citizenVaultId == 0 && vaultId != 0) {
+               }
+               if (citizenVaultId == 0 && vaultId != 0) {
                        _assetTransferFrom(VAULT, msg.sender, address(this), vaultId);
                        citizenStatus.hasVault = true;
                        citizenStatus.stakedVaultId = vaultId;
```


## No need to cache as the cached value is being referenced only once

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L625-L645
### NeoTokyoStaker.sol.getCreditYield(): IGenericGetter(S1_CITIZEN) and IGenericGetter(VAULT) should not be cached
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
625:	function getCreditYield (
626:		uint256 _citizenId,
627:		uint256 _vaultId
628:	) public view returns (string memory) {

630:		// Retrieve the total reward rate of this S1 Citizen.
631:		IGenericGetter citizen = IGenericGetter(S1_CITIZEN);
632:		uint256 rewardRate = citizen.getRewardRateOfTokenId(_citizenId);
633:		if (rewardRate == 0) {
634:			revert CitizenDoesNotExist(_citizenId);
635:		}

637:		// Retrieve the credit rate multiplier of any associated Vault.
638:		IGenericGetter vault = IGenericGetter(VAULT);
639:		string memory vaultMultiplier = (_vaultId != 0)
640:			? vault.getCreditMultiplier(_vaultId)
641:			: "";
		
643:		// Deduce the original Identity credit yield.
644:		return identityCreditYield[rewardRate][vaultMultiplier];
645:	}
```

```diff
@@ -628,16 +629,14 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        ) public view returns (string memory) {

                // Retrieve the total reward rate of this S1 Citizen.
-               IGenericGetter citizen = IGenericGetter(S1_CITIZEN);
-               uint256 rewardRate = citizen.getRewardRateOfTokenId(_citizenId);
+               uint256 rewardRate = IGenericGetter(S1_CITIZEN).getRewardRateOfTokenId(_citizenId);
                if (rewardRate == 0) {
                        revert CitizenDoesNotExist(_citizenId);
                }

                // Retrieve the credit rate multiplier of any associated Vault.
-               IGenericGetter vault = IGenericGetter(VAULT);
                string memory vaultMultiplier = (_vaultId != 0)
-                       ? vault.getCreditMultiplier(_vaultId)
+                       ? IGenericGetter(VAULT).getCreditMultiplier(_vaultId)
                        : "";

                // Deduce the original Identity credit yield.
```


https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L659-L671
### NeoTokyoStaker.sol.getConfiguredVaultMultiplier(): IGenericGetter(VAULT) should not be cached
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
659:	function getConfiguredVaultMultiplier (
660:		uint256 _vaultId
661:	) public view returns (uint256) {

663:		// Retrieve the credit rate multiplier of the Vault.
664:		IGenericGetter vault = IGenericGetter(VAULT);
665:		string memory vaultMultiplier = (_vaultId != 0)
666:			? vault.getCreditMultiplier(_vaultId)
667:			: "";

669:		// Deduce the configured Vault multiplier.
670:		return vaultCreditMultiplier[vaultMultiplier];
671:	}
```


```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..f72bf08 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -661,9 +661,8 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        ) public view returns (uint256) {

                // Retrieve the credit rate multiplier of the Vault.
-               IGenericGetter vault = IGenericGetter(VAULT);
                string memory vaultMultiplier = (_vaultId != 0)
-                       ? vault.getCreditMultiplier(_vaultId)
+                       ? IGenericGetter(VAULT).getCreditMultiplier(_vaultId)
                        : "";
```

## Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1124-L1174
### NeoTokyoStaker.sol.\_stakeLP(): stakerLPPosition[msg.sender] should be cached in local storage
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
	function _stakeLP (

		if (stakerLPPosition[msg.sender].multiplier == 0) { //@audit: 1st access - happy path
			stakerLPPosition[msg.sender].multiplier = timelockMultiplier;//@audit: 2nd access - happy path

		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) {
			revert MismatchedTimelock();
		}

		unchecked {
			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;


			// Update the caller's LP token stake.
			stakerLPPosition[msg.sender].timelockEndTime =
				block.timestamp + timelockDuration;//@audit: 3rd access - happy path
			stakerLPPosition[msg.sender].amount += amount;//@audit: 4th access - happy path
			stakerLPPosition[msg.sender].points += points;//@audit: 5th access - happy path
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1409-L1454
### NeoTokyoStaker.sol.claimReward(): lastRewardTime\[\_recipient] should be cached in local storage
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
1409:	function claimReward (

1432:		// Record the current time as the beginning time for checking rewards.
1433:		lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;//@audit: 1st access
1434:		lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;//@audit: 2nd access
1435:		lastRewardTime[_recipient][AssetType.LP] = block.timestamp;//@audit: 3rd access
```



## Not in scope
The following are not in scope, **Calling them out as we can pack with no precision loss**
## Pack structs by putting data types in ascending size

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L359-L365
### NeoTokyoStaker.sol.StakedS1Citizen{}: Pack timelockEndTime with hasVault.(Saves 1 SLOT: 2k Gas)
**As uint256 timelockEndTime is a timestamp, we can reduce the size from uint256 to maybe uint128 with no side effects. This would still be safe against overflows**
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
359:	struct StakedS1Citizen {
360:		uint256 stakedBytes;
361:		uint256 timelockEndTime;
362:		uint256 points;
363:		uint256 stakedVaultId;
364:		bool hasVault;
365:	}
```
Reduce the size of the variable `timelockEndTime` and pack it with `hasVault`
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..b9ea61e 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -358,10 +358,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        */
        struct StakedS1Citizen {
                uint256 stakedBytes;
-               uint256 timelockEndTime;
+               uint128 timelockEndTime;
+               bool hasVault;
                uint256 points;
                uint256 stakedVaultId;
-               bool hasVault;
        }

        /**
```


https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L462-L469
### NeoTokyoStaker.sol.StakedS1CitizenOutput{}: Pack timelockEndTime with hasVault (Save 1 SLOT: 2k Gas)
**As uint256 timelockEndTime is a timestamp, we can reduce the size from uint256 to maybe uint128 with no side effects. This would still be safe against overflows**
```solidity
File: /contracts/staking/NeoTokyoStaker.sol
462:	struct StakedS1CitizenOutput {
463:		uint256 citizenId;
464:		uint256 stakedBytes;
465:		uint256 timelockEndTime;
466:		uint256 points;
467:		uint256 stakedVaultId;
468:		bool hasVault;
469:	}
```

```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..8de274f 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -462,10 +462,11 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        struct StakedS1CitizenOutput {
                uint256 citizenId;
                uint256 stakedBytes;
-               uint256 timelockEndTime;
+               uint128 timelockEndTime;
+               bool hasVault;
                uint256 points;
                uint256 stakedVaultId;
-               bool hasVault;
+
        }
```

