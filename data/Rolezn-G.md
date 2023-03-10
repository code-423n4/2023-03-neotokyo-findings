## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | Use calldata instead of memory for function parameters | 1 | 300 |
| [GAS&#x2011;2](#GAS&#x2011;2) | Do not calculate constants | 1 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 2 | 56 |
| [GAS&#x2011;4](#GAS&#x2011;4) | Empty Blocks Should Be Removed Or Emit Something | 2 | - |
| [GAS&#x2011;5](#GAS&#x2011;5) | Using `delete` statement can save gas | 8 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Use `assembly` to write address storage values | 3 | - |
| [GAS&#x2011;7](#GAS&#x2011;7) | Use hardcoded address instead `address(this)` | 8 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | Optimize names to save gas | 2 | 44 |
| [GAS&#x2011;9](#GAS&#x2011;9) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 8 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | Structs can be packed into fewer storage slots by editing time variables | 5~8 | 10000~16000 |
| [GAS&#x2011;11](#GAS&#x2011;11) | Public Functions To External | 3 | - |
| [GAS&#x2011;12](#GAS&#x2011;12) | Save gas with the use of specific import statements | 6 | - |
| [GAS&#x2011;13](#GAS&#x2011;13) | Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It | 1 | - |
| [GAS&#x2011;14](#GAS&#x2011;14) | Use v4.8.1 OpenZeppelin contracts | 2 | - |

Total: 52 contexts over 14 issues

## Gas Optimizations



### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of
memory is more optimal.

Consider the following generic example:
```
contract C {
	function add(uint[] memory arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above example, the dynamic array arr has the storage location
memory. When the function gets called externally, the array values are
kept in calldata and copied to memory during ABI decoding (using the
opcode calldataload and mstore). And during the for loop, arr[i]
accesses the value in memory using a mload. However, for the above
example this is inefficient. Consider the following snippet instead:
```
contract C {
	function add(uint[] calldata arr) external returns (uint sum) {
		uint length = arr.length;
		for (uint i = 0; i < arr.length; i++) {
		    sum += arr[i];
		}
	}
}
```
In the above snippet, instead of going via memory, the value is directly
read from calldata using calldataload. That is, there are no
intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with
copying value from calldata to memory in a for loop. Each iteration
would cost at least 60 gas. In the latter example, this can be
completely avoided. This will also reduce the number of instructions and
therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument
is only read.

Note that in older Solidity versions, changing some function arguments
from memory to calldata may cause "unimplemented feature error".
This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples
Note: The following pattern is prevalent in the codebase:
```
function f(bytes memory data) external {
	(...) = abi.decode(data, (..., types, ...));
}
```
Here, changing to bytes calldata will decrease the gas. The total
savings for this change across all such uses would be quite
significant.

#### <ins>Proof Of Concept</ins>


```solidity
function getStakerPosition (
		address _staker,
		AssetType _assetType
	) external view returns (uint256[] memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L689






### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

#### <ins>Proof Of Concept</ins>

```solidity
203: uint256 constant private _BYTES_PER_POINT = 200 * 1e18;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L203





#### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
784: revert(string(data));
812: revert(string(data));

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L784

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L812








### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```solidity
1250: default {}
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1250

```solidity
1693: default {}
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1693







### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Using `delete` statement can save gas

#### <ins>Proof Of Concept</ins>

```solidity
1517: stakedCitizen.stakedBytes = 0;
1518: stakedCitizen.timelockEndTime = 0;
1519: stakedCitizen.points = 0;
1521: stakedCitizen.stakedVaultId = 0;

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1517

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1518

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1519

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1521



```solidity
1582: stakedCitizen.stakedBytes = 0;
1583: stakedCitizen.timelockEndTime = 0;
1584: stakedCitizen.points = 0;

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1582-L1584

```solidity
1635: lpPosition.multiplier = 0;

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1635





### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Use `assembly` to write address storage values

#### <ins>Proof Of Concept</ins>

```solidity
1231: _b = _stakeBytes;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1231

```solidity
1232: _lp = _stakeLP;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1232

```solidity
1678: _lp = _withdrawLP;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1678





### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Use hardcode address instead `address(this)`

Instead of using `address(this)`, it is more gas-efficient to pre-calculate and use the hardcoded `address`. Foundry's script.sol and solmate's `LibRlp.sol` contracts can help achieve this.

References: 
https://book.getfoundry.sh/reference/forge-std/compute-create-address 

https://twitter.com/transmissions11/status/1518507047943245824

#### <ins>Proof Of Concept</ins>

```solidity
897: _assetTransferFrom(S1_CITIZEN, msg.sender, address(this), citizenId);
927: _assetTransferFrom(VAULT, msg.sender, address(this), vaultId);

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L897

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L927



```solidity
1010: _assetTransferFrom(S2_CITIZEN, msg.sender, address(this), citizenId);

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1010

```solidity
1057: _assetTransferFrom(BYTES, msg.sender, address(this), amount);

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1057

```solidity
1137: _assetTransferFrom(LP, msg.sender, address(this), amount);

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1137

```solidity
1485: address(this),
1492: _assetTransferFrom(S1_CITIZEN, address(this), msg.sender, citizenId);

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1485

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1492



```solidity
1557: _assetTransferFrom(S2_CITIZEN, address(this), msg.sender, citizenId);

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1557



#### <ins>Recommended Mitigation Steps</ins>

Use hardcoded `address`




### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

All in-scope contracts

#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.



### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
977: pool.totalPoints += citizenStatus.points;

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L977

```solidity
1029: pool.totalPoints += citizenStatus.points;

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1029

```solidity
1078: citizenStatus.stakedBytes += amount;
1079: citizenStatus.points += bonusPoints;
1080: pool.totalPoints += bonusPoints;
1078: citizenStatus.stakedBytes += amount;
1079: citizenStatus.points += bonusPoints;
1080: pool.totalPoints += bonusPoints;

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1078

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1079

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1080

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1078

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1079

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1080



```solidity
1160: stakerLPPosition[msg.sender].amount += amount;
1161: stakerLPPosition[msg.sender].points += points;
1164: pool.totalPoints += points;

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1160

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1161

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1164








### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Structs can be packed into fewer storage slots by editing time variables

The following structs contain time variables that are unlikely to ever reach max uint256 then it is possible to set them as uint64, saving gas slots.
In total, it is possible to save around 5-8 storage slots

#### <ins>Proof Of Concept</ins>

```solidity
359: struct StakedS1Citizen {
		uint256 stakedBytes;
		uint256 timelockEndTime;
		uint256 points;
		uint256 stakedVaultId;
		bool hasVault;
	}
```
https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L359

Can save one storage slot by editing `timelockEndTime` to `uint64` and changing to:
```solidity
359: struct StakedS1Citizen {
		uint256 stakedBytes;
		uint256 points;
		uint256 stakedVaultId;
		bool hasVault;
		uint64 timelockEndTime;
	}
```

The same applies to:
```solidity
462: struct StakedS1CitizenOutput {
		uint256 citizenId;
		uint256 stakedBytes;
		uint256 timelockEndTime;
		uint256 points;
		uint256 stakedVaultId;
		bool hasVault;
	}
```
https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L462

For the following, each struct can save between 1-2 storage slot by changing `timelockEndTime` to `uint64` and the rest to a smaller uint such as `uint128` if `uint256` if the value is unlikely to go over max `uin128`

394: struct StakedS2Citizen {
		uint256 stakedBytes;
		uint256 timelockEndTime;
		uint256 points;
	}
423: struct LPPosition {
		uint256 amount;
		uint256 timelockEndTime;
		uint256 points;
		uint256 multiplier;
	}
486: struct StakedS2CitizenOutput {
		uint256 citizenId;
		uint256 stakedBytes;
		uint256 timelockEndTime;
		uint256 points;
	}

```



https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L394

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L423

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L486







### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```solidity
function getCreditYield (
		uint256 _citizenId,
		uint256 _vaultId
	) public view returns (string memory) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L625

```solidity
function getConfiguredVaultMultiplier (
		uint256 _vaultId
	) public view returns (uint256) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L659

```solidity
function getPoolReward (
		AssetType _assetType,
		address _recipient
	) public view returns (uint256, uint256) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1264






### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
7: import "../access/PermitControl.sol";
8: import "../interfaces/IByteContract.sol";
9: import "../interfaces/IStaker.sol";

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/BYTES2.sol#L7-L9

```solidity
6: import "../access/PermitControl.sol";
7: import "../interfaces/IByteContract.sol";
8: import "../interfaces/IGenericGetter.sol";

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L6-L8




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Help The Optimizer By Saving A Storage Variable's Reference Instead Of Repeatedly Fetching It

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

#### <ins>Proof Of Concept</ins>


```solidity
1502: if (citizenId == oldPosition[stakedIndex]) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1502






### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> Use v4.8.1 OpenZeppelin contracts

The upcoming v4.8.1 version of OpenZeppelin provides many small gas optimizations.
https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.8.1

#### <ins>Proof Of Concept</ins>

```solidity
    "@openzeppelin/contracts-upgradeable": "^4.4.2"
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/../package.json#L9

```solidity
    "@openzeppelin/contracts": "^4.3.1"
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/../package.json#L23



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.8.1






