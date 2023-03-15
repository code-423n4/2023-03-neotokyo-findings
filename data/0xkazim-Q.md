# Findings Summary

| ID     | Title                                                  | Severity |
| ------ | ------------------------------------------------------ | -------- |
| [L-01] | contracts should inherit their interfacese             | low      |
| [L-02] | lack of zero address check may cause problems          | low      |
| [L-03] | state change must be done in two step                  | low      |
| [L-04] | Gas griefing/theft is possible on unsafe external call | low      |
| [L-05] | no check if the burn amount is zero or not             | low      |
| [L-06] | A single point of failure                              | low      |

# [L-01] contracts should inherit their interfacese

## Description

Contract implementations should inherit their interfaces. Extending an interface ensures that all function signatures are correct,
and catches mistakes introduced (e.g. through errant keystrokes)

## context

```solidity

file: staking/BYTES2.sol

contract BYTES2 is PermitControl, ERC20("BYTES", "BYTES") {

```

## Recommendations

add the interface to the contract :

```solidity

contract BYTES2 is PermitControl, IByteContract , ERC20("BYTES", "BYTES") {

```

# [L-02] lack of zero address check may cause problems

## Description

Checking addresses against zero-address during initialization or during setting is a security best-practice.
However, such checks are missing in address variable initializations/changes in many places.

## context

set zero-address check for immutable variables in the constructor

```solidity

file: contract/staking/BYTES2.sol

constructor  (
		address _bytes,
		address _s1Citizen,
		address _staker,
		address _treasury
	) payable{
		//@audit set zero address check for immutable variables
		BYTES1 = _bytes;
		S1_CITIZEN = _s1Citizen;
		STAKER = _staker;
		TREASURY = _treasury;
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L75-L85

```solidity

file: contract/staking/NeoTokyoStaker.sol

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
        //@audit lack of zero-address check
		BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
		LP = _lpToken;
		IDENTITY = _identity;
		VAULT = _vault;
		VAULT_CAP = _vaultCap;
		NO_VAULT_CAP = _noVaultCap;
	}	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588-L606

you can add zero-address checks to these functions for better secuirty practice :

```solidity
file: contract/staking/BYTES2.sol

function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L162-L166

```solidity
file: contract/staking/BYTES2.sol

function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L173-L177

## Recommendations

add zero-address check for the functions and contructor above :

```solidity
require(exampleAddress != address(0), "invailed address")`
```

# [L-03] state change must be done in two step

## Description

Critical Address Changes Should Use Two-step Procedure
The critical procedures should be two step process.

## context

it is always better to doing the state changes or address change with two step:

```solidity

file: contract/staking/BYTES2.sol

function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L162-L166

```solidity
file: contract/staking/BYTES2.sol

function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L173-L177

## Recommendations

it's better and safer to change the critical state changes/set using two steps.

# [L-04] Gas griefing/theft is possible on unsafe external call

## Description

return data (bool success,) has to be stored due to EVM architecture, if in a usage like below, ‘out’ and ‘outsize’ values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided.
this may cause a medium risk but i put it as a low because it set to low in a previous report, check this link for similar sevirity:

https://code4rena.com/reports/2023-01-biconomy#l-04-gas-griefingtheft-is-possible-on-unsafe-external-call

## context

```solidity

file: contract/staking/NeoTokyoStaker.sol

	function _assetTransferFrom (
		address _asset,
		address _from,
		address _to,
		uint256 _idOrAmount
	) private {
		// @audit gas griefing
		(bool success, bytes memory data) =
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_FROM_SELECTOR,
					_from,
					_to,
					_idOrAmount
				)
			);

		// Revert if the low-level call fails.
		if (!success) {
			revert(string(data));
		}
	}

```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L766-L780

```solidity
file: contract/staking/NeoTokyoStaker.sol

function _assetTransfer (
		address _asset,
		address _to,
		uint256 _amount
	) private {
		//@audit same above
		(bool success, bytes memory data) =
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_SELECTOR,
					_to,
					_amount
				)
			);

		// Revert if the low-level call fails.
		if (!success) {
			revert(string(data));
		}
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L796-L814

## Recommendations

you can do the transactions above similar to this one :

```solidity
bool success;
assembly {
    success := call(3000, receiver, amount, 0, 0, 0, 0)
}
```

# [L-05] no check if the burn amount is zero or not

## Description

if the amount is zero so the unhecked block will divided zero on 3 and we use gas for nothing !
if we set zero we may pass the `_burn ` checks, i know it is passed by only permit but it's better
to avoid this happen because it's seting by human and it means it can be set with 0 balance to burn !

## context

```solidity

file: contract/staking/BYTES2.sol

	function burn (
		address _from,
		uint256 _amount
	) hasValidPermit(UNIVERSAL, BURN) external {

		_burn(_from, _amount);

		/*
			We are aware that this math does not round perfectly for all values of
			`_amount`. We don't care.
		*/
		uint256 treasuryShare;
		unchecked {
			treasuryShare = _amount * 2 / 3;
		}
		_mint(TREASURY, treasuryShare);
	}

```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L140-L155

## Recommendations

add zero balance checks to avoid this to happen or add mapping to get permit addressess balance and check if their balance is zero or not

# [L-06] A single point of failure

## Description

the `PermitControl.sol`(out of scope) use ownable and can be used to delegate specific rights to
external addresses, that's mean Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case,
the attacker can cause serious damage by adding another contract address to the `PermitContro` to the project due to important functions. and this ,ay cause vulnarability to this function below and users who have invested in project will suffer high financial losses

## context

```solidity

file: contract/staking/BYTES2.sol

	function burn (
		address _from,
		uint256 _amount
	) hasValidPermit(UNIVERSAL, BURN) external {

```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L140-L155

```solidity
file: contract/staking/BYTES2.sol

function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L162-L164

```solidity
file: contract/staking/BYTES2.sol

function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L173-L175

```solidity
file: contract/staking/NeoTokyoStaker.sol

function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP)

```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1708-L1710

```solidity
file: contract/staking/NeoTokyoStaker.sol

function lockLP () external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		lpLocked = true;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1721-L1723

```solidity
file: contract/staking/NeoTokyoStaker.sol

function configureTimelockOptions (
		AssetType _assetType,
		uint256[] memory _timelockIds,
		uint256[] memory _encodedSettings
	) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS)
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1737-L1741

```solidity
file: contract/staking/NeoTokyoStaker.sol

function configureIdentityCreditYields (
		uint256[] memory _citizenRewardRates,
		string[] memory _vaultRewardRates,
		string[] memory _identityCreditYields
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1760-L1764

```solidity
file: contract/staking/NeoTokyoStaker.sol

function configureIdentityCreditPoints (
		string[] memory _identityCreditYields,
		uint256[] memory _points
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1783-L1787

```solidity
file: contract/staking/NeoTokyoStaker.sol

function configureVaultCreditMultipliers (
		string[] memory _vaultCreditMultipliers,
		uint256[] memory _multipliers
	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1802-L1805

```solidity
file: contract/staking/NeoTokyoStaker.sol

function configurePools (
		PoolConfigurationInput[] memory _inputs
	) hasValidPermit(UNIVERSAL, CONFIGURE_POOLS) external
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1819-L1821

```solidity
file: contract/staking/NeoTokyoStaker.sol

ffunction configureCaps (
		uint256 _vaultedCap,
		uint256 _unvaultedCap
	) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external {
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1851-L1854

## Recommendations

Add a time lock to critical functions. Admin-only functions that change critical parameters should emit events and have timelocks.
