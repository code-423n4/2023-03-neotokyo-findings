## Low Risk Issues Summary
| Number |Issues Details |
|:--:|:-------|
|[L-01]| Missing checks for address(0x0)  
|[L-02]| Missing event for critical parameter change

***
## Non-Critical Issues Summary
| Number |Issues Details |
|:--:|:-------|
|[NC-01]| Use stable pragma statement 
|[NC-02]| Use named imports instead of plain `import FILE.SOL`
|[NC-03]| Update external dependency to latest version
|[NC-04]| Solidity compiler optimizations can be problematic
|[NC-05]| Constant values such as a call to keccak256(), should used to immutable rather than constant
|[NC-06]| Constants should be defined rather than using magic numbers
|[NC-07]| You can use named parameters in mapping types

***

## [L-01] MISSING CHECKS FOR ADDRESS(0X0)

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

```solidity
BYTES2.sol
81: BYTES1 = _bytes;
82: S1_CITIZEN = _s1Citizen;
83: STAKER = _staker;
84: TREASURY = _treasury;

114: function getReward (
115:     address _to

140: function burn (
141:     address _from,


NeoTokyoStaker.sol
588: constructor (
589:     address _bytes,
590:     address _s1Citizen,
591:     address _s2Citizen,
592:     address _lpToken,
593:     address _identity,
594:     address _vault,
595:     uint256 _vaultCap,
596:     uint256 _noVaultCap
		) {
```

#### Recommended Mitigation Steps

Consider adding explicit zero-address validation on input parameters of address type.
***

## [L-02] MISSING EVENT FOR CRITICAL PARAMETER CHANGE

Emitting events allows monitoring activities with off-chain monitoring tools.
```solidity
BYTES2.sol
114: function getReward (
115:        address _to
116:    ) external {

140: function burn (
141:        address _from,
142:        uint256 _amount
143:    ) hasValidPermit(UNIVERSAL, BURN) external {
```
***

## [NC-01] Use stable pragma statement

Using a floating pragma statement `^0.8.19` is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode.
***

## [NC-02] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT FILE.SOL`

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";
***

## [NC-03] Update external dependency to latest version

The latest version is 4.8.2

package.json:
```solidity
9: "@openzeppelin/contracts-upgradeable": "^4.4.2",
```
***

## [NC-04] Solidity compiler optimizations can be problematic

hardhat.config.js
```solidity
compilers: {
    solc: {
      version: '0.8.11', and version: '0.8.19',
      settings: {
        optimizer: {
          enabled: true,
          runs: 200,
          details: {
	          yul: true
          },
        },
      },
    },
  },
```
 
Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.
***

## [NC-05] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USED TO IMMUTABLE RATHER THAN CONSTANT
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

```solidity
BYTES2.sol
37: bytes32 public constant BURN = keccak256("BURN");
40: bytes32 public constant ADMIN = keccak256("ADMIN");

NeoTokyoStaker.sol
206: bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
209: bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
214: bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
217: bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
220: bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```
***

## [NC-06] Constants should be defined rather than using magic numbers

```solidity
BYTES2.sol
152: treasuryShare = _amount * 2 / 3;

NeoTokyoStaker.sol
962: uint256 timelockDuration = _timelock >> 128;
1113: (seasonId << 128) + citizenId,
1140: uint256 timelockDuration = _timelock >> 128;
1205: if (uint8(_assetType) > 4) {
1389: uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
```
***

## [NC-07] You can use named parameters in mapping types

From Solidity [0.8.18](https://blog.soliditylang.org/2023/02/01/solidity-0.8.18-release-announcement/) you can use named parameters in mapping types. 
***