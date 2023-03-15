## Low Risk Issues List
### Issue 
## [L-01] Gas griefing/theft is possible on unsafe external call
### Total: 01 contexts over 01 issues
## [L-01] Gas griefing/theft is possible on unsafe external call
return data (bool success,) has to be stored due to EVM architecture, if in a usage like below, ‘out’ and ‘outsize’ values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided
https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
		(bool success, bytes memory data) = 
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_FROM_SELECTOR,
					_from,
					_to, 
					_idOrAmount
				)
			);
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L772-L780

## Non-Critical Issues List
### Issue 
## [N-01] Use a single file for all system-wide constants
## [N-02] Need Fuzzing test
## [N-03] Function writing that does not comply with the Solidity Style Guide
## [N-04] For modern and more readable code; update import usages
## [N-05] Include return parameters in NatSpec comments
## [N-06] Upgradable openzeppelin contracts are recommended
## [N-07] LOCK PRAGMAS TO SPECIFIC COMPILER VERSION
## [N-08] PRAGMA VERSION^0.8.19 VERSION TOO RECENT TO BE TRUSTED.
## [N-09] MISSING EVENT FOR CRITICAL PARAMETERS INIT AND CHANGE
## [N-10] Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.
### Total: 56 contexts over 10 issues
## [N-01] Use a single file for all system-wide constants
### Context: 14
There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

#### constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol
```
37:	bytes32 public constant BURN = keccak256("BURN");
40: 	bytes32 public constant ADMIN = keccak256("ADMIN");
```
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
191:	bytes4 constant private _TRANSFER_FROM_SELECTOR = 0x23b872dd;
194:	bytes4 constant private _TRANSFER_SELECTOR = 0xa9059cbb;
197:	uint256 constant private _PRECISION = 1e12;
200:	uint256 constant private _DIVISOR = 100;
203:	uint256 constant private _BYTES_PER_POINT = 200 * 1e18;
206:	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
209:	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
214:	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
217: 	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
220:	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```

## [N-02] Need Fuzzing test
### Context: 30
### Description
In total 2 contracts, 30 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.
### Recommendation
Use should fuzzing test like Echidna.
As Alberto Cuesta Canada said:
Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now.
https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol
```
151:		unchecked {
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L151

#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
728:			unchecked { i++; }
743:			unchecked { i++; }
967:		unchecked {
1021:		unchecked {
1076:			unchecked {
1097:			unchecked {
1154:		unchecked {
1282:					unchecked {
1291:					unchecked {
1297:				unchecked {
1330:							unchecked {
1341:							unchecked {
1354:								unchecked {
1369:						unchecked { j++; }
1377:					unchecked {
1383:				unchecked { i++; }
1387:			unchecked {
1440:		unchecked {
1509:			unchecked { stakedIndex++; }
1514:		unchecked {
1574:			unchecked { stakedIndex++; }
1579:		unchecked {
1622:		unchecked {
1744:			unchecked { ++i; }
1771:			unchecked { ++i; }
1789:			unchecked { ++i; }
1808:			unchecked { ++i; }
1838:				unchecked { j++; }
1840:			unchecked { ++i; }
```

## [N-03] Function writing that does not comply with the Solidity Style Guide
### Context: 01
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
### Description
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

## [N-04] For modern and more readable code; update import usages
### Context: 02
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol
### Description:
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.
This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.
### Recommendation:
import {contract1 , contract2} from "filename.sol";
A good example from the ArtGobblers project;
```
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

## [N-05] Include return parameters in NatSpec comments
### Context: 02
#### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol
### Description
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html
### Recommendation
Include return parameters in NatSpec comments

## [N-06] Upgradable openzeppelin contracts are recommended
Context: 03
### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol
```
4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L4-L5

### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
```
4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L4
### Recommendation
https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts/token/ERC20
https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/security/ReentrancyGuardUpgradeable.sol

## [N-07] LOCK PRAGMAS TO SPECIFIC COMPILER VERSION
### Context: 02
01: https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L2
02: https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L2
### Description:
Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.
https://swcregistry.io/docs/SWC-103
### Recommendation:
Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
solidity-specific/locking-pragmas

## [N-08] PRAGMA VERSION^0.8.19 VERSION TOO RECENT TO BE TRUSTED.
https://github.com/ethereum/solidity/blob/develop/Changelog.md
Unexpected bugs can be reported in recent versions;
Risks related to recent releases
Risks of complex code generation changes
Risks of new language features
Risks of known bugs
Use a non-legacy and more battle-tested version

## [N-09] MISSING EVENT FOR CRITICAL PARAMETERS INIT AND CHANGE
### Context: 02
### File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol
```
	function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L162-L166
```
	function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L173-L177
### Description:
Events help non-contract tools to track changes, and events prevent users from being surprised by changes
### Recommendation:
Add Event-Emit

## [N-10] Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.
File: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

## Suggestion
### Issue
## [S-01] Project Upgrade and Stop Scenario should be
## [S-02] GENERATE PERFECT CODE HEADERS EVERY TIME
### Total:  02 issues
## [S-01] Project Upgrade and Stop Scenario should be
At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation. This can also be called an ” EMERGENCY STOP (CIRCUIT BREAKER) PATTERN “.
https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

## [S-02] GENERATE PERFECT CODE HEADERS EVERY TIME
### Description:
I recommend using header for Solidity code layout and readability
https://github.com/transmissions11/headers
```
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```