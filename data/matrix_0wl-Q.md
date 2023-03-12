# Summary

## Low Risk and Non-Critical Issues

## Non-Critical Issues

|       | Issue                                                                                    |
| ----- | :--------------------------------------------------------------------------------------- |
| NC-1  | ADD EIP-2981 NFT ROYALTY STANDART SUPPORT                                                |
| NC-2  | ADD TO BLACKLIST FUNCTION                                                                |
| NC-3  | GENERATE PERFECT CODE HEADERS EVERY TIME                                                 |
| NC-4  | USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS                                          |
| NC-5  | FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES                                  |
| NC-6  | CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT |
| NC-7  | MISSING CHECKS FOR `ADDRESS(0)`                                                          |
| NC-8  | NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS                                        |
| NC-9  | NO SAME VALUE INPUT CONTROL                                                              |
| NC-10 | SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC                                       |
| NC-11 | FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE                      |
| NC-12 | PRAGMA VERSION^0.8.19 VERSION TOO RECENT TO BE TRUSTED                                   |

### [NC-1] ADD EIP-2981 NFT ROYALTY STANDART SUPPORT

#### Description:

Consider adding EIP-2981 NFT Royalty Standard to the project: https://eips.ethereum.org/EIPS/eip-2981

Royalty (Copyright – EIP 2981):

- Fixed % royalties: For example, 6% of all sales go back to artists
- Declining royalties: There may be a continuous decline in sales based on time or any other variable.
- Dynamic royalties: Varies over time or sales amount
- Upgradeable royalties: Allows a legal entity or NFT owner to change any copyright
- Incremental royalties: No royalties, for example when sold for less than $100
- Managed royalties: Funds are owned by a DAO, imagine the recipient is a DAO treasury
- Royalties to different people: Collectors and artists can even use royalties, not specific to a particular personality

### [NC-2] ADD TO BLACKLIST FUNCTION

#### Description:

NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.

Here is the project example; Manifold

Manifold Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

#### Recommended Mitigation Steps:

Add to Blacklist function and modifier.

```solidity
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }

```

### [NC-3] GENERATE PERFECT CODE HEADERS EVERY TIME

#### Description:

I recommend using header for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

### [NC-4] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS

#### Description:

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes.

Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

37:     bytes32 public constant BURN = keccak256("BURN");

40:     bytes32 public constant ADMIN = keccak256("ADMIN");

```

```solidity
File: NeoTokyoStaker.sol

206:     bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

209:     bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(

214:     bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

217:     bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

220:     bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```

### [NC-5] FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES

#### Description:

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.

This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "../access/PermitControl.sol";

8: import "../interfaces/IByteContract.sol";

9: import "../interfaces/IStaker.sol";

```

```solidity
File: NeoTokyoStaker.sol

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

6: import "../access/PermitControl.sol";

7: import "../interfaces/IByteContract.sol";

8: import "../interfaces/IGenericGetter.sol";

```

#### Recommended Mitigation Steps:

`import {contract1 , contract2} from "filename.sol";` OR Use specific imports syntax per solidity docs recommendation.

### [NC-6] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### Description:

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

37:     bytes32 public constant BURN = keccak256("BURN");

40:     bytes32 public constant ADMIN = keccak256("ADMIN");

```

```solidity
File: NeoTokyoStaker.sol

206:     bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

209:     	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
210:		"CONFIGURE_TIMELOCKS"
211:	);

214:     bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

217:     bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

220:     bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```

### [NC-7] MISSING CHECKS FOR `ADDRESS(0)`

#### Description:

0 address control should be done in these parts:

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

81:         BYTES1 = _bytes;

82:         S1_CITIZEN = _s1Citizen;

83:         STAKER = _staker;

84:         TREASURY = _treasury;

```

```solidity
File: NeoTokyoStaker.sol

598:         BYTES = _bytes;

599:         S1_CITIZEN = _s1Citizen;

600:         S2_CITIZEN = _s2Citizen;

601:         LP = _lpToken;

602:         IDENTITY = _identity;

603:         VAULT = _vault;

```

#### Recommended Mitigation Steps:

Add code like this: `if (oracle == address(0)) revert ADDRESS_ZERO();` or `require(address(\_VARIABLE) != address(0), "Address cannot be zero");

### [NC-8] NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS

#### Description:

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.

In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html.

### [NC-9] NO SAME VALUE INPUT CONTROL

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

81:         BYTES1 = _bytes;

82:         S1_CITIZEN = _s1Citizen;

83:         STAKER = _staker;

84:         TREASURY = _treasury;

```

```solidity
File: NeoTokyoStaker.sol

598:         BYTES = _bytes;

599:         S1_CITIZEN = _s1Citizen;

600:         S2_CITIZEN = _s2Citizen;

601:         LP = _lpToken;

602:         IDENTITY = _identity;

603:         VAULT = _vault;

604:         VAULT_CAP = _vaultCap;

605:         NO_VAULT_CAP = _noVaultCap;

```

#### Recommended mitigation steps:

Add code like this; `if (oracle == _oracle revert ADDRESS_SAME();`

### [NC-10] SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC

#### Description:

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

#### **Proof Of Concept**

```solidity
File: hardhat.config.ts:

26:	solidity: {
		compilers: [
			{
				version: '0.8.11',
				settings: {
					optimizer: {
						enabled: true,
						runs: 200,
						details: {
							yul: true
						}
					}
				}
			},
			{
				version: '0.8.19',
				settings: {
					optimizer: {
						enabled: true,
						runs: 200,
						details: {
							yul: true
						}
					}
				}
			}
		]
	},
```

#### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.

Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [NC-11] FUNCTION WRITING THAT DOES NOT COMPLY WITH THE SOLIDITY STYLE GUIDE

#### Context:

NeoTokyoStaker.sol

#### Description:

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private, within a grouping, place the view and pure functions last

### [NC-12] PRAGMA VERSION^0.8.19 VERSION TOO RECENT TO BE TRUSTED

#### Description:

For security, it is best practice to use the latest Solidity version. For the security fix list in the versions; https://github.com/ethereum/solidity/blob/develop/Changelog.md

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

2: pragma solidity ^0.8.19;

```

```solidity
File: NeoTokyoStaker.sol

2: pragma solidity ^0.8.19;

```

### [NC-13] UNUSED IMPORTS

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

### [NC-14] LINES ARE TOO LONG

#### Description:

Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.

[Reference](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length)

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

900:         StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];

949:             string memory class = IGenericGetter(IDENTITY).getClass(identityId);

1013:         StakedS2Citizen storage citizenStatus = stakedS2[msg.sender][citizenId];

1310:             uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];

1466:         StakedS1Citizen storage stakedCitizen = stakedS1[msg.sender][citizenId];

1541:         StakedS2Citizen storage stakedCitizen = stakedS2[msg.sender][citizenId];

1623:             uint256 points = (((amount * 100) / 1e18) * lpPosition.multiplier) / _DIVISOR;

```

## Low Issues

|     | Issue                                                          |
| --- | :------------------------------------------------------------- |
| L-1 | DOS WITH BLOCK GAS LIMIT                                       |
| L-2 | FUNCTIONS THAT CREATE DIRTY BITS                               |
| L-3 | LOSS OF PRECISION DUE TO ROUNDING                              |
| L-4 | REVERT MESSAGES ARE TOO SHORT AND UNCLEAR                      |
| L-5 | TIMESTAMP DEPENDENCE                                           |
| L-6 | ACCOUNT EXISTENCE CHECK FOR LOW-LEVEL CALLS                    |
| L-7 | USE `_SAFEMINT` INSTEAD OF `_MINT`                             |
| L-8 | NOT USING THE LATEST VERSION OF OPENZEPPELIN FROM DEPENDENCIES |

### [L-1] DOS WITH BLOCK GAS LIMIT

#### Description:

Programming patterns such as looping over arrays of unknown size may lead to DoS when the gas cost of execution exceeds the block gas limit.

https://swcregistry.io/docs/SWC-128

This loop could drain all user gas and revert;

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

1499:         for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {

1564:         for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {

```

### [L-2] FUNCTIONS THAT CREATE DIRTY BITS

#### Description:

This explanation should be added in the NatSpec comments of this function that sends ether with call;

Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed. Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer.

[code4arena example](https://code4rena.com/reports/2022-11-non-fungible#l-04--_returndust--function-create-dirty-bits)

#### **Proof Of Concept**

#### Recommended Mitigation Steps:

Add this comment to \_returnDust function:

`/// @dev Use with caution! Some functions in this contract knowingly create dirty bits at the destination of the free memory pointer. Note that this code probably isn’t secure or a good use case for assembly because a lot of memory management and security checks are bypassed.`

```solidity
File: NeoTokyoStaker.sol

833:         assembly {

1236:         assembly {

1682:         assembly {

```

### [L-3] LOSS OF PRECISION DUE TO ROUNDING

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

152:             treasuryShare = _amount * 2 / 3;

```

```solidity
File: NeoTokyoStaker.sol

968:            citizenStatus.points =
				identityPoints * vaultMultiplier * timelockMultiplier /
				_DIVISOR / _DIVISOR;

1022:             citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;

1077:                 uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

1098:                 uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

1155:             uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

1388:                 uint256 share = points * _PRECISION / pool.totalPoints * totalReward;

1389:                 uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);

1623:             uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

```

#### Recommended Mitigation Steps:

We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.

### [L-4] REVERT MESSAGES ARE TOO SHORT AND UNCLEAR

#### Description:

The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features. Error definitions should be added to the reVERT block, not exceeding 32 bytes.

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

784:             revert(string(data));

812:             revert(string(data));

```

#### Recommended Mitigation Steps:

Error definitions should be added to the revert block, not exceeding 32 bytes or we should use custom errors.

### [L-5] TIMESTAMP DEPENDENCE

#### Description:

Contracts often need access to time values to perform certain types of functionality. Values such as block.timestamp, and block.number can give you a sense of the current time or a time delta, however, they are not safe to use for most purposes.

In the case of block.timestamp, developers often attempt to use it to trigger time-dependent events. As Ethereum is decentralized, nodes can synchronize time only to some degree. Moreover, malicious miners can alter the timestamp of their blocks, especially if they can gain advantages by doing so. However, miners cant set a timestamp smaller than the previous one (otherwise the block will be rejected), nor can they set the timestamp too far ahead in the future. Taking all of the above into consideration, developers cant rely on the preciseness of the provided timestamp.

Reference: https://swcregistry.io/docs/SWC-116

Reference: (https://github.com/kadenzipfel/smart-contract-vulnerabilities/blob/master/vulnerabilities/timestamp-dependence.md)

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

971:             citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

1023:             citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

1159:                 block.timestamp + timelockDuration;

1215:         if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {

1329:                         if (block.timestamp <= window.startTime) {

1331:                                 uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;

1356:                                     block.timestamp - lastPoolRewardTime;

1378:                         uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;

1433:         lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;

1434:         lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;

1435:         lastRewardTime[_recipient][AssetType.LP] = block.timestamp;

1467:         if (block.timestamp < stakedCitizen.timelockEndTime) {

1542:         if (block.timestamp < stakedCitizen.timelockEndTime) {

1605:         if (block.timestamp < lpPosition.timelockEndTime) {

```

### [L-6] ACCOUNT EXISTENCE CHECK FOR LOW-LEVEL CALLS

#### Description:

Low-level calls `call`/`delegatecall`/`staticcall` return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed.

https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-callsn

#### **Proof Of Concept**

```solidity
File: NeoTokyoStaker.sol

773:         (bool success, bytes memory data) = _asset.call(

802:         (bool success, bytes memory data) = _asset.call(

```

#### Recommended Mitigation Steps:

In addition to the zero-address checks, add a check to verify that <address>.code.length > 0

### [L-7] USE `_SAFEMINT` INSTEAD OF `_MINT`

#### Description:

According to openzepplin’s ERC721, the use of `_mint` is discouraged, use `safeMint` whenever possible.

https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-mint-address-uint256-

#### **Proof Of Concept**

```solidity
File: BYTES2.sol

102:         _mint(msg.sender, _amount);

124:             _mint(_to, reward);

127:             _mint(TREASURY, daoCommision);

154:         _mint(TREASURY, treasuryShare);

```

#### Recommended Mitigation Steps:

Use `_safeMint` whenever possible instead of `_mint`

### [L-8] NOT USING THE LATEST VERSION OF OPENZEPPELIN FROM DEPENDENCIES

#### Description:

The package.json configuration file says that the project is using 4.4.2/4.3.1 etc. of OpenZeppelin which has a not last update version.

Patched versions for @openzeppelin/contracts and @openzeppelin/contracts-upgradeable is 4.4.1.

That is why @openzeppelin/contracts version 4.3.1 is vulnerable.

https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-9c22-pwxw-p6hx

#### **Proof Of Concept**

```solidity
File: package.json

9:  "@openzeppelin/contracts-upgradeable": "^4.4.2",

23: "@openzeppelin/contracts": "^4.3.1",

```

#### Recommended Mitigation Steps:

Use patched versions. Latest non vulnerable version 4.4.1.
