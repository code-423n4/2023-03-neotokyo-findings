## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Add to `blacklist` function | 1 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Function can risk gas exhaustion on large receipt calls due to multiple mandatory loops | 2 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Possible rounding issue | 10 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Contracts are not using their OZ Upgradeable counterparts | 3 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Unbounded loop | 2 |
| [LOW&#x2011;6](#LOW&#x2011;6) | Upgrade OpenZeppelin Contract Dependency | 2 |

Total: 20 contexts over 6 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Add a timelock to critical functions | 2 |
| [NC&#x2011;2](#NC&#x2011;2) | Avoid Floating Pragmas: The Version Should Be Locked | 2 |
| [NC&#x2011;3](#NC&#x2011;3) | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 5 |
| [NC&#x2011;4](#NC&#x2011;4) | Variable Names That Consist Of All Capital Letters Should Be Reserved For Const/immutable Variables | 3 |
| [NC&#x2011;5](#NC&#x2011;5) | Constants Should Be Defined Rather Than Using Magic Numbers | 6 |
| [NC&#x2011;6](#NC&#x2011;6) | Constants in comparisons should appear on the left side | 5 |
| [NC&#x2011;7](#NC&#x2011;7) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 2 |
| [NC&#x2011;8](#NC&#x2011;8) | Function writing that does not comply with the Solidity Style Guide  | 2 |
| [NC&#x2011;9](#NC&#x2011;9) | Use `delete` to Clear Variables | 8 |
| [NC&#x2011;10](#NC&#x2011;10) | NatSpec return parameters should be included in contracts | 1 |
| [NC&#x2011;11](#NC&#x2011;11) | NatSpec comments should be increased in contracts | 1 |
| [NC&#x2011;12](#NC&#x2011;12) | Non-usage of specific imports | 6 |
| [NC&#x2011;13](#NC&#x2011;13) | Empty blocks should be removed or emit something | 8 |
| [NC&#x2011;14](#NC&#x2011;14) | Use `private constant` consistently | 5 |

Total: 56 contexts over 14 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Add to `blacklist` function

NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.

Here is the project example; Manifold

Manifold Contract https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```solidity
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```


#### <ins>Recommended Mitigation Steps</ins>
Add to Blacklist function and modifier.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Function can risk gas exhaustion on large receipt calls due to multiple mandatory loops

The function contains loops over arrays that the user cannot influence. 
In any call for the function, users spend gas to iterate over the array. There are loops in the following functions that iterate all array values. Which could risk gas exhaustion on large array calls.

This risk is small, but could be lessened somewhat by allowing separate calls for small loops.
Consider allowing partial calls via array arguments, or detecting expensive call bundles and only partially iterating over them. Since the logic already filters out calls, this would make the later loops smaller.

#### <ins>Proof Of Concept</ins>


```solidity
1459: function _withdrawS1Citizen () private {
        ...
        uint256[] storage oldPosition = _stakerS1Position[msg.sender];
        for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {

            
            if (citizenId == oldPosition[stakedIndex]) {
                if (stakedIndex != oldPosition.length - 1) {
                    oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
                }
                oldPosition.pop();
                break;
            }
            unchecked { stakedIndex++; }
        }
        ...
    }

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1459




### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Possible rounding issue

Division by large numbers may result in the result not rounding properlly, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

#### <ins>Proof Of Concept</ins>


```solidity
152: treasuryShare = _amount * 2 / 3;

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/BYTES2.sol#L152






### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/BYTES2.sol#L4-L5

```solidity
4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L4



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.
See https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts for list of available upgradeable contracts




### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Upgrade OpenZeppelin Contract Dependency

An outdated OZ version is used (which has known vulnerabilities, see: https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

#### <ins>Proof Of Concept</ins>

Require dependencies to be at least version of 4.8.1 to include critical vulnerability patches.

```solidity
    "@openzeppelin/contracts-upgradeable": "^4.4.2"
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/../package.json#L9

```solidity
    "@openzeppelin/contracts": "^4.3.1"
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/../package.json#L23



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.8.1 via `>=4.8.1`



## Non Critical Issues



### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Avoid Floating Pragmas: The Version Should Be Locked

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### <ins>Proof Of Concept</ins>

```solidity
Found usage of floating pragmas ^0.8.19 of Solidity in [BYTES2.sol]
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/BYTES2.sol#L2

```solidity
Found usage of floating pragmas ^0.8.19 of Solidity in [NeoTokyoStaker.sol]
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L2






### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables

Variable names with all capital letters should be restricted to be used only with `constant` or `immutable` variables.
If the variable needs to be different based on which class it comes from, a `view`/`pure` function should be used instead (e.g. like <a href="https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59">this</a>).

#### <ins>Proof Of Concept</ins>

```solidity
49: address public STAKER;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/BYTES2.sol#L49

```solidity
52: address public TREASURY;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/BYTES2.sol#L52

```solidity
232: address public LP;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L232

```solidity
249: uint256 public VAULT_CAP;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L249

```solidity
255: uint256 public NO_VAULT_CAP;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L255





### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Constants Should Be Defined Rather Than Using Magic Numbers

#### <ins>Proof Of Concept</ins>


```solidity
1022: citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1022

```solidity
1155: uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
```
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1205

```solidity
1623: uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1623






### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Constants in comparisons should appear on the left side

Doing so will prevent <a href="https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html">typo bugs</a>

#### <ins>Proof Of Concept</ins>

```solidity
633: if (rewardRate == 0) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L633

```solidity
1144: if (stakerLPPosition[msg.sender].multiplier == 0) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1144

```solidity
1221: if (timelockOption == 0) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1221

```solidity
1472: if (stakedCitizen.timelockEndTime == 0) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1472

```solidity
1547: if (stakedCitizen.timelockEndTime == 0) {
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1547










#### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
784: revert(string(data));
812: revert(string(data));

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L784

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L812








### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Function writing that does not comply with the Solidity Style Guide

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

#### <ins>Proof Of Concept</ins>

- NeoTokyoStaker.sol






### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Use `delete` to Clear Variables

`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at `x`.

The `delete` key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with `delete` statements.

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








### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> NatSpec return parameters should be included in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html
#### <ins>Proof Of Concept</ins>


- NeoTokyoStaker.sol


#### <ins>Recommended Mitigation Steps</ins>

Include return parameters in NatSpec comments

Recommendation Code Style: (from Uniswap3)

```solidity
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

For example see NeoTokyoStaker.getPoolReward()
```solidity
    /**
        This function supports retrieving the reward and tax earned by a particular 
        `_recipient` on a specific pool of type `_assetType`.

        @param _assetType The type of the asset to calculate rewards for.
        @param _recipient The recipient of the reward.
    */
    function getPoolReward (
        AssetType _assetType,
        address _recipient
    ) public view returns (uint256, uint256) {
```







### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> NatSpec comments should be increased in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

#### <ins>Proof Of Concept</ins>



- NeoTokyoStaker.sol

For example see NeoTokyoStaker._stakeBytes()

```solidity
    /**
        A private function for managing the staking of BYTES into a Citizen.
    */
    function _stakeBytes (
        uint256
    ) private {
```


#### <ins>Recommended Mitigation Steps</ins>

NatSpec comments should be increased in contracts



### <a href="#Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Non-usage of specific imports

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






### <a href="#Summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Empty blocks should be removed or emit something

#### <ins>Proof Of Concept</ins>

```solidity
193: ) external {
    }
207: ) external {
    }
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/BYTES2.sol#L193

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/BYTES2.sol#L207



```solidity
1250: default {}
1693: default {}
```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1250

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L1693





### <a href="#Summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> Use `private constant` consistently

Replace `constant private` with `private constant`.

#### <ins>Proof Of Concept</ins>

```solidity
191: bytes4 constant private _TRANSFER_FROM_SELECTOR = 0x23b872dd;
194: bytes4 constant private _TRANSFER_SELECTOR = 0xa9059cbb;
197: uint256 constant private _PRECISION = 1e12;
200: uint256 constant private _DIVISOR = 100;
203: uint256 constant private _BYTES_PER_POINT = 200 * 1e18;

```

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L191

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L194

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L197

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L200

https://github.com/code-423n4/2023-03-neotokyo/tree/main/contracts/staking/NeoTokyoStaker.sol#L203








