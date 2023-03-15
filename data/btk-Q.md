| Total Low issues |
|------------------|

| Risk   | Issues Details                                                                                              | Number        |
|--------|-------------------------------------------------------------------------------------------------------------|---------------|
| [L-01] | Loss of precision due to rounding                                                                           | 8             |
| [L-02] | Missing checks for `address(0)`                                                                             | 2             |
| [L-03] | Array lengths not checked                                                                                   | 4             |
| [L-04] | Solidity compiler optimizations can be problematic                                                          | 1             |
| [L-05] | Value is not validated to be different than the existing one                                                | 2             |
| [L-06] | Use `immutable` instead of `constant` for values such as a call to `keccak256()`                            | 7             |

| Total Non-Critical issues |
|---------------------------|

| Risk    | Issues Details                                                                                              | Number        |
|---------|-------------------------------------------------------------------------------------------------------------|---------------|
| [NC-01] | Include return parameters in NatSpec comments                                                               | 1             |
| [NC-02] | Non-usage of specific imports                                                                               | 9             |
| [NC-03] | Lack of event emit                                                                                          | 2             |
| [NC-04] | Function writing does not comply with the `Solidity Style Guide`                                            | 1             |
| [NC-05] | Add `address(0)` check for the critical changes                                                             | 1             |
| [NC-06] | Use a more recent version of OpenZeppelin dependencies                                                      | 1             |
| [NC-07] | Constants in comparisons should appear on the left side                                                     | 11            |
| [NC-08] | Contracts should have full test coverage                                                                    | All Contracts |
| [NC-09] | Need Fuzzing test                                                                                           | All Contracts |
| [NC-10] | Generate perfect code headers every time                                                                    | All Contracts |
| [NC-11] | Lock pragmas to specific compiler version                                                                   | 2             |
| [NC-12] | Consider using `delete` rather than assigning zero to clear values                                          | 8             |
| [NC-13] | For functions, follow Solidity standard naming conventions                                                  | All Contracts |
| [NC-14] | Add NatSpec Mapping comment                                                                                 | 12            |
| [NC-15] | Use SMTChecker                                                                                              |               |

## [L-01] Loss of precision due to rounding

#### Description

Loss of precision due to the nature of arithmetics and rounding errors.

#### Lines of code 

```solidity
			citizenStatus.points =
				identityPoints * vaultMultiplier * timelockMultiplier /
				_DIVISOR / _DIVISOR;
```

- [NeoTokyoStaker.sol#L968-L970](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L968-L970)

```solidity
			citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
```

- [NeoTokyoStaker.sol#L1022](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1022)

```solidity
				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
```

- [NeoTokyoStaker.sol#L1077](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1077)

```solidity
				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
```

- [NeoTokyoStaker.sol#L1098](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1098)

```solidity
			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
```

- [NeoTokyoStaker.sol#L1155](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155)

```solidity
				uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
```

- [NeoTokyoStaker.sol#L1388](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388)

```solidity
				uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
```

- [NeoTokyoStaker.sol#L1389](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1389)

```solidity
			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
```

- [NeoTokyoStaker.sol#L1623](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1623)

## [L-02] Missing checks for `address(0)`

#### Description

Check of `address(0)` to protect the code from `(0x0000000000000000000000000000000000000000)` address problem just in case. This is best practice or instead of suggesting that they verify `_address != address(0)`, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

#### Lines of code 

```solidity
	constructor (
		address _bytes,
		address _s1Citizen,
		address _staker,
		address _treasury
	) {
		BYTES1 = _bytes;
		S1_CITIZEN = _s1Citizen;
		STAKER = _staker;
		TREASURY = _treasury;
	}
```

- [BYTES2.sol#L75-L85](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-L85)

```solidity
	constructor (
		address _bytes,
		address _s1Citizen,
		address _s2Citizen,
		address _lpToken,
		address _identity,
		address _vault,
		uint256 _vaultCap,
		uint256 _noVaultCap
	) {
		BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
		LP = _lpToken;
		IDENTITY = _identity;
		VAULT = _vault;
		VAULT_CAP = _vaultCap;
		NO_VAULT_CAP = _noVaultCap;
	}
```

- [NeoTokyoStaker.sol#L588-L606](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L606)

#### Recommended Mitigation Steps

Add checks for `address(0)` when assigning values to address state variables.	

## [L-03] Array lengths not checked

#### Description

If one of the arrays is a shorter length than the other arrays: 

 - The additional values in the other arrays may be ignored.
 - The users operations may not be fully executed. 
 - This could lead to transfers with unexpected results, which would be better served by reverting.

#### Lines of code 

```solidity
	function configureTimelockOptions (
		AssetType _assetType,
		uint256[] memory _timelockIds,
		uint256[] memory _encodedSettings
	) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
		for (uint256 i; i < _timelockIds.length; ) {
			timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];
			unchecked { ++i; }
		}
	}
```

- [NeoTokyoStaker.sol#L1737-L1746](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737-L1746)

```solidity
	function configureIdentityCreditYields (
		uint256[] memory _citizenRewardRates, 
		string[] memory _vaultRewardRates,
		string[] memory _identityCreditYields
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
		for (uint256 i; i < _citizenRewardRates.length; ) {
			identityCreditYield[
				_citizenRewardRates[i]
			][
				_vaultRewardRates[i]
			] = _identityCreditYields[i];
			unchecked { ++i; }
		}
	}
```

- [NeoTokyoStaker.sol#L1760-L1773](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1760-L1773)

```solidity
	function configureIdentityCreditPoints (
		string[] memory _identityCreditYields,
		uint256[] memory _points
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
		for (uint256 i; i < _identityCreditYields.length; ) {
			identityCreditPoints[_identityCreditYields[i]] = _points[i];
			unchecked { ++i; }
		}
	}
```

- [NeoTokyoStaker.sol#L1783-L1791](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1783-L1791)

```solidity
	function configureVaultCreditMultipliers (
		string[] memory _vaultCreditMultipliers,
		uint256[] memory _multipliers
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
		for (uint256 i; i < _vaultCreditMultipliers.length; ) {
			vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];
			unchecked { ++i; }
		}
	}
```

- [NeoTokyoStaker.sol#L1802-L1810](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1802-L1810)

#### Recommended Mitigation Steps

Check that the arrays length are the same.

## [L-04] Solidity compiler optimizations can be problematic

#### Description

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

#### Lines of code

```javaScript
				settings: {
					optimizer: {
						enabled: true,
						runs: 200, 
						details: {
							yul: true 
						}
					}
```

- [hardhat.config.js#L30-L37](https://github.com/code-423n4/2023-03-neotokyo/blob/main/hardhat.config.js#L30-L37)

#### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug. Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## [L-05] Value is not validated to be different than the existing one

#### Description

While the value is validated to be in the constant bounds, it is not validated to be different than the existing one. Queueing the same value will cause multiple abnormal events to be emitted, will ultimately result in a no-op situation.

#### Lines of code 

```solidity
	function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}
```

- [BYTES2.sol#L162-L166](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162-L166)

```solidity
	function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}
```

- [BYTES2.sol#L173-L177](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173-L177)

#### Recommended Mitigation Steps

Add a `require()` statement to check that the new value is different than the current one.

## [L-06] Use `immutable` instead of `constant` for values such as a call to `keccak256()`

#### Description

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

#### Lines of code 

```solidity
	bytes32 public constant BURN = keccak256("BURN");
```

- [BYTES2.sol#L37](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37)

```solidity
	bytes32 public constant ADMIN = keccak256("ADMIN");
```

- [BYTES2.sol#L40](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L40)

```solidity
	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
```

- [NeoTokyoStaker.sol#L206](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206)

```solidity
	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);
```

- [NeoTokyoStaker.sol#L209-L211](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L209-L211)

```solidity
	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
```

- [NeoTokyoStaker.sol#L214](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L214)

```solidity
	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
```

- [NeoTokyoStaker.sol#L217](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L217)

```solidity
	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```

- [NeoTokyoStaker.sol#L220](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L220)

#### Recommended Mitigation Steps

Use `immutable` instead of `constants` 

## [NC-01] Include return parameters in NatSpec comments

#### Description

If Return parameters are declared, you must prefix them with `/// @return`.
Some code analysis programs do analysis by reading [NatSpec](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html) details, if they can't see the `@return` tag, they do incomplete analysis.

#### Lines of code 

```solidity
	) public view returns (uint256, uint256) {
```

- [NeoTokyoStaker.sol#L1267](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1267)

#### Recommended Mitigation Steps

Include `@return` parameters in NatSpec comments

## [NC-02] Non-usage of specific imports

#### Description

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace. Instead, the Solidity docs recommend specifying imported symbols explicitly. 

- https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

#### Lines of code 

```solidity
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
```

- [BYTES2.sol#L4](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L4)

```solidity
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```

- [BYTES2.sol#L5](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L5)

```solidity
import "../access/PermitControl.sol";
```

- [BYTES2.sol#L7](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L7)

```solidity
import "../interfaces/IByteContract.sol";
```

- [BYTES2.sol#L8](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L8)

```solidity
import "../interfaces/IStaker.sol";
```

- [BYTES2.sol#L9](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L9)

```solidity
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
```

- [NeoTokyoStaker.sol#L4](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L4)

```solidity
import "../access/PermitControl.sol";
```

- [NeoTokyoStaker.sol#L6](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L6)

```solidity
import "../interfaces/IByteContract.sol";
```

- [NeoTokyoStaker.sol#L7](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L7)

```solidity
import "../interfaces/IGenericGetter.sol";
```

- [NeoTokyoStaker.sol#L8](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L8)

#### Recommended Mitigation Steps

Use specific imports syntax per solidity docs recommendation.

## [NC-03] Lack of event emit

#### Description

The below methods do not emit an event when the state changes, something that it's very important for dApps and users.

#### Lines of code 

```solidity
	function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}
```

- [BYTES2.sol#L162-L166](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162-L166)

```solidity
	function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}
```

- [BYTES2.sol#L173-L177](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173-L177)

#### Recommended Mitigation Steps

Emit event.

## [NC-04] Function writing does not comply with the `Solidity Style Guide`

#### Description

Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

Functions should be grouped according to their visibility and ordered:

- `constructor()`
- `receive()`  
- `fallback()`  
- `external / public / internal / private`
- `view / pure`

#### Lines of code 

- [NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)

#### Recommended Mitigation Steps

Follow Solidity Style Guide.

## [NC-05] Add `address(0)` check for the critical changes

#### Description

Check of the address for the critical changes to protect the code from address(0) problem in case of mistakes.

#### Lines of code 

```solidity
	function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}
```

- [BYTES2.sol#L162-L166](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162-L166)

```solidity
	function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}
```

- [BYTES2.sol#L173-L177](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173-L177)

#### Recommended Mitigation Steps

Add address(0) check.

## [NC-06] Use a more recent version of OpenZeppelin dependencies

#### Description

For security, it is best practice to use the latest OpenZeppelin version.

#### Lines of code 

```javaScript
    "@openzeppelin/contracts-upgradeable": "^4.4.2",
```

- [package.json#L9](https://github.com/code-423n4/2023-03-neotokyo/blob/main/package.json#L9)

#### Recommended Mitigation Steps

Old version of OpenZeppelin is used `(4.4.2)`, newer version can be used [`(4.8.0)`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable).

## [NC-07] Constants in comparisons should appear on the left side

#### Description

Constants in comparisons should appear on the left side, doing so will prevent typo [bug](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html).

```solidity
		if (stakerLPPosition[msg.sender].multiplier == 0) {
```

#### Lines of code 

```solidity
		if (rewardRate == 0) {
```

- [NeoTokyoStaker.sol#L633](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L633)

```solidity
		} else if (citizenVaultId != 0 && vaultId == 0) {
```

- [NeoTokyoStaker.sol#L917](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L917)

```solidity
		} else if (citizenVaultId == 0 && vaultId != 0) {
```

- [NeoTokyoStaker.sol#L926](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L926)

```solidity
			if (citizenStatus.timelockEndTime == 0) {
```

- [NeoTokyoStaker.sol#L1071](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1071)

```solidity
			if (citizenStatus.timelockEndTime == 0) {
```

- [NeoTokyoStaker.sol#L1092](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1092)

```solidity
		if (stakerLPPosition[msg.sender].multiplier == 0) {
```

- [NeoTokyoStaker.sol#L1144](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1144)

```solidity
		if (_pools[_assetType].rewardCount == 0) {
```

- [NeoTokyoStaker.sol#L1210](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1210)

```solidity
		if (timelockOption == 0) {
```

- [NeoTokyoStaker.sol#L1221](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1221)

```solidity
		if (stakedCitizen.timelockEndTime == 0) {
```

- [NeoTokyoStaker.sol#L1472](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1472)

```solidity
		if (stakedCitizen.timelockEndTime == 0) {
```

- [NeoTokyoStaker.sol#L1547](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1547)

```solidity
		if (lpPosition.amount == 0) {
```

- [NeoTokyoStaker.sol#L1634](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1634)

#### Recommended Mitigation Steps

Constants should appear on the left side:

```solidity
		if (0 == stakerLPPosition[msg.sender].multiplier) {
```

## [NC-08] Contracts should have full test coverage

#### Description

While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

```
- What is the overall line coverage percentage provided by your tests?: 80
```

#### Recommended Mitigation Steps

Line coverage percentage should be 100%.

## [NC-09] Need Fuzzing test

#### Description

In total 2 contracts, 30 `unchecked{}` are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking)

#### Recommended Mitigation Steps

Use should fuzzing test like Echidna. As Alberto Cuesta Canada said: Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now.

Ref: https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

## [NC-10] Generate perfect code headers every time

#### Description

I recommend using header for Solidity code layout and readability

> Ref: https://github.com/transmissions11/headers

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking)

#### Recommended Mitigation Steps

```
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

## [NC-11] Lock pragmas to specific compiler version

#### Description

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally. 

> Ref: https://swcregistry.io/docs/SWC-103

#### Lines of code 

```solidity
pragma solidity ^0.8.19;
```

- [NeoTokyoStaker.sol#L2](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2)

```solidity
pragma solidity ^0.8.19;
```

- [BYTES2.sol#L2](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2)

#### Recommended Mitigation Steps

[Ethereum Smart Contract Best Practices](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/): Lock pragmas to specific compiler version. 

## [NC-12] Consider using `delete` rather than assigning zero to clear values    

#### Description

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

#### Lines of code 

```solidity
		stakedCitizen.stakedBytes = 0;
```

- [NeoTokyoStaker.sol#L1517](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517)

```solidity
		stakedCitizen.timelockEndTime = 0;
```

- [NeoTokyoStaker.sol#L1518](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1518)

```solidity
		stakedCitizen.points = 0;
```

- [NeoTokyoStaker.sol#L1519](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1519)

```solidity
		stakedCitizen.stakedVaultId = 0;
```

- [NeoTokyoStaker.sol#L1521](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1521)

```solidity
		stakedCitizen.stakedBytes = 0;
```

- [NeoTokyoStaker.sol#L1582](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1582)

```solidity
		stakedCitizen.timelockEndTime = 0;
```

- [NeoTokyoStaker.sol#L1583](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1583)

```solidity
		stakedCitizen.points = 0;
```

- [NeoTokyoStaker.sol#L1584](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1584)

```solidity
			lpPosition.multiplier = 0;
```

- [NeoTokyoStaker.sol#L1635](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1635)

#### Recommended Mitigation Steps

Use the `delete` keyword.

## [NC-13] For functions, follow Solidity standard naming conventions

#### Description

The protocol don't follow solidity standard naming convention.

> Ref: https://docs.soliditylang.org/en/v0.8.17/style-guide.html#naming-conventions

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking)

#### Recommended Mitigation Steps

Follow solidity standard [naming convention](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#naming-conventions).

## [NC-14] Add NatSpec Mapping comment  

#### Description

Add NatSpec comments describing mapping keys and values.

```solidity
	mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
```

#### Lines of code 

```solidity
	mapping ( AssetType => mapping ( uint256 => uint256 )) public	timelockOptions;
```

- [NeoTokyoStaker.sol#L280](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L280)

```solidity
		mapping ( uint256 => RewardWindow ) rewardWindows;
```

- [NeoTokyoStaker.sol#L312](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L312)

```solidity
	mapping ( AssetType => PoolData ) private _pools;
```

- [NeoTokyoStaker.sol#L316](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L316)

```solidity
	mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
```

- [NeoTokyoStaker.sol#L319](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L319)

```solidity
	mapping ( uint256 => mapping ( string => string )) public identityCreditYield;
```

- [NeoTokyoStaker.sol#L326](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L326)

```solidity
	mapping ( string => uint256 ) public identityCreditPoints;
```

- [NeoTokyoStaker.sol#L329](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L329)

```solidity
	mapping ( string => uint256 ) public vaultCreditMultiplier;
```

- [NeoTokyoStaker.sol#L332](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L332)

```solidity
	mapping ( address => mapping( uint256 => StakedS1Citizen )) public stakedS1;
```

- [NeoTokyoStaker.sol#L372](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L372)

```solidity
	mapping ( address => uint256[] ) private _stakerS1Position;
```

- [NeoTokyoStaker.sol#L378](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L378)

```solidity
	mapping ( address => mapping( uint256 => StakedS2Citizen )) public stakedS2;
```

- [NeoTokyoStaker.sol#L405](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L405)

```solidity
	mapping ( address => uint256[] ) private _stakerS2Position;
```

- [NeoTokyoStaker.sol#L411](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L411)

```solidity
	mapping ( address => LPPosition ) public stakerLPPosition;
```

- [NeoTokyoStaker.sol#L434](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L434)

#### Recommended Mitigation Steps

```solidity
/// @dev address(caller) => AssetType(Asset) => uint256(Time)
	mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
```

## [NC-15] Use SMTChecker

#### Description

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

> Ref: https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19   

