# No check if same length in functions with arrays as its parameter

The 
- `configureVaultCreditMultipliers()`
- `configureTimelockOptions()`
- `configureIdentityCreditYields()`
- `configureIdentityCreditPoints()`

Those function in `NeoTokyoStaker.sol` doesn't check the (array) input if it contains same length.

```solidity
File: NeoTokyoStaker.sol
1802: 	function configureVaultCreditMultipliers (
1803: 		string[] memory _vaultCreditMultipliers,
1804: 		uint256[] memory _multipliers
1805: 	) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1806: 		for (uint256 i; i < _vaultCreditMultipliers.length; ) {
1807: 			vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];
1808: 			unchecked { ++i; }
1809: 		}
1810: 	}
```

To prevent any misconfiguration or invalid behaviour we can make sure the length of both array equals by adding, for example:

```js
require(_vaultCreditMultipliers.length == _multipliers.length);
```

# Doesn't check if `_amount` > 0

In `Bytes2.sol` file, there are some function which doesn't check if `_amount` is > 0. By adding this required condition, we can prevent any unnecessary transaction, thus if the `_amount` is 0 it will simply revert.

```diff
File: BYTES2.sol
093: 	function upgradeBytes (
094: 		uint256 _amount
095: 	) external {
++      require(_amount > 0, "Zero Amount");
096: 		if (IERC20(BYTES1).balanceOf(msg.sender) < _amount) {
097: 			revert DoNotHaveEnoughOldBytes(_amount);
098: 		}
099:
100: 		// Burn the original BYTES 1.0 tokens and mint replacement BYTES 2.0.
101: 		IByteContract(BYTES1).burn(msg.sender, _amount);
102: 		_mint(msg.sender, _amount);
103:
104: 		// Emit the upgrade event.
105: 		emit BytesUpgraded(msg.sender, _amount);
106: 	}
...
140: 	function burn (
141: 		address _from,
142: 		uint256 _amount
143: 	) hasValidPermit(UNIVERSAL, BURN) external {
++      require(_amount > 0, "Zero Amount");
144: 		_burn(_from, _amount);
145:
146: 		/*
147: 			We are aware that this math does not round perfectly for all values of
148: 			`_amount`. We don't care.
149: 		*/
150: 		uint256 treasuryShare;
151: 		unchecked {
152: 			treasuryShare = _amount * 2 / 3;
153: 		}
154: 		_mint(TREASURY, treasuryShare);
155: 	}
```

# No emit on important storage variable changes

When changing storage variable commonly a setter function it's recommended to emit an event, meanwhile the protocol most of the setter function didn't emit an event. Emitting events allows monitoring activities with off-chain monitoring tools.

```solidity
File: BYTES2.sol
162: 	function changeStakingContractAddress (
163: 		address _staker
164: 	) hasValidPermit(UNIVERSAL, ADMIN) external {
165: 		STAKER = _staker;
166: 	}
...
173: 	function changeTreasuryContractAddress (
174: 		address _treasury
175: 	) hasValidPermit(UNIVERSAL, ADMIN) external {
176: 		TREASURY = _treasury;
177: 	}
```



# Not using named import

All import using plain `import 'file.sol`' instead use named import.

```solidity
Plain import
File: BYTES2.sol
4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
6:
7: import "../access/PermitControl.sol";
8: import "../interfaces/IByteContract.sol";
9: import "../interfaces/IStaker.sol";

File: NeoTokyoStaker.sol
4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
5:
6: import "../access/PermitControl.sol";
7: import "../interfaces/IByteContract.sol";
8: import "../interfaces/IGenericGetter.sol";
```

These plain / default import:

- have no control over what is imported
- cannot avoid naming conflicts at all
- harder to discover where a particular name (function, variables, etc) is defined

It's better and recommended to only import what we need, for example:

```solidity
Named import (example)
2: import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
3: import {EnumerableSet} from "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
```

# Unspecific Compiler version pragma

```solidity
pragma solidity ^0.8.19;
```

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.
