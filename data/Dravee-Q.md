**Overview**
Risk Rating | Number of issues
--- | ---
Low Risk | 7
Non-Critical Risk | 9

**Table of Contents**

- [1. Low Risk Issues](#1-low-risk-issues)
  - [1.1. The `uint8(_assetType) > 4` check is wrong and unnecessary due to a vacuous truth](#11-the-uint8_assettype--4-check-is-wrong-and-unnecessary-due-to-a-vacuous-truth)
  - [1.2. `NeoTokyoStaker._stakeLP`: Check that stakes will earn points](#12-neotokyostaker_stakelp-check-that-stakes-will-earn-points)
  - [1.3. Using `transferFrom` on ERC721 tokens](#13-using-transferfrom-on-erc721-tokens)
  - [1.4. `_assetTransferFrom` is used both for ERC721 and ERC20](#14-_assettransferfrom-is-used-both-for-erc721-and-erc20)
  - [1.5. Known vulnerabilities exist in currently used `@openzeppelin/contracts` version](#15-known-vulnerabilities-exist-in-currently-used-openzeppelincontracts-version)
  - [1.6. No event emitted when updating a state variable](#16-no-event-emitted-when-updating-a-state-variable)
  - [1.7. `NeoTokyoStaker.getStakerPosition()` is clunky](#17-neotokyostakergetstakerposition-is-clunky)
- [2. Non-Critical Issues](#2-non-critical-issues)
  - [2.1. `BYTES2.getReward()`'s naming is confusing](#21-bytes2getrewards-naming-is-confusing)
  - [2.2. Avoid using magic numbers instead of the existing enum's values](#22-avoid-using-magic-numbers-instead-of-the-existing-enums-values)
  - [2.3. `"@openzeppelin/contracts"` should be a dependency, not a dev-dependency](#23-openzeppelincontracts-should-be-a-dependency-not-a-dev-dependency)
  - [2.4. Duplicated code](#24-duplicated-code)
  - [2.5. The `unchecked` statements are undocumented](#25-the-unchecked-statements-are-undocumented)
  - [2.6. Use `delete` instead of setting every fields to their default value](#26-use-delete-instead-of-setting-every-fields-to-their-default-value)
  - [2.7. Add commented parameter names](#27-add-commented-parameter-names)
  - [2.8. Import declarations should import specific identifiers, rather than the whole file](#28-import-declarations-should-import-specific-identifiers-rather-than-the-whole-file)
  - [2.9. Function ordering does not follow the Solidity style guide](#29-function-ordering-does-not-follow-the-solidity-style-guide)

# 1. Low Risk Issues

## 1.1. The `uint8(_assetType) > 4` check is wrong and unnecessary due to a vacuous truth

The following `uint8(_assetType) > 4` checks never be verified due to a vacuous truth:

- <https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205-L1207>

```solidity
  // Validate that the asset being staked is of a valid type.
  if (uint8(_assetType) > 4) {
   revert InvalidAssetType(uint256(_assetType));
  }
```

- <https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668-L1670>

```solidity
  if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
   revert InvalidAssetType(uint256(_assetType));
  }
```

Indeed, the `enum AssetType` is limited to an index of 3:

```solidity
 /**
  This enum tracks each type of asset that may be operated on with this 
  staker.

  @param S1_CITIZEN A staked Neo Tokyo S1 Citizen.
  @param S2_CITIZEN A staked Neo Tokyo S2 Citizen.
  @param BYTES A set of staked BYTES ERC-20 token.
  @param LP Staked BYTES-ETH LP ERC-20 token.
 */
 enum AssetType {
  S1_CITIZEN, //@audit index 0
  S2_CITIZEN, //@audit index 1
  BYTES, //@audit index 2
  LP //@audit index 3
 }
```

And the following will always revert if using anything that is `>= 4`:

```solidity
function stake (
    AssetType _assetType, //@audit-ok will revert with anything >= 4

function withdraw (
    AssetType _assetType, //@audit-ok will revert with anything >= 4
```

Therefore, as `uint8(_assetType)` is at most equal to `3` in a transaction, it's impossible to ever verify `uint8(_assetType) > 4`

Furthermore, `uint8(_assetType) > 4` is a wrong check as it doesn't exclude `4` which isn't even a reachable value for `AssetType`. If the input parameter in the method wouldn't have reverted for some reason with the input `4`, both `uint8(_assetType) > 4` conditions would have let a wrong index of `4` slip through.

I suggest deleting those as they can never happen. But if you insist on them existing just to be reassured, consider either replacing those by `uint8(_assetType) >= 4` or `uint8(_assetType) > 3`, as these would be the right checks.

## 1.2. `NeoTokyoStaker._stakeLP`: Check that stakes will earn points

In the following [LP stake scenario](https://github.com/code-423n4/2023-03-neotokyo/blob/main/test/NeoTokyoStaker.test.js#L760-L766), Bob is staking `10e18` LP tokens and [earning 4000 points as a result](https://github.com/code-423n4/2023-03-neotokyo/blob/main/test/NeoTokyoStaker.test.js#L785).

If we tweak the test so that Bob stakes 1/1000 of that amount (`0.01e18`), the result would be 4 points.

However, if we tweak further the test so that Bob stakes `0.0099999e18`, the result is 0 point. It would've been legitimate to believe that the minimum would've been `0.0025e18` for 1 point but it instead directly drops to 0 under `0.01e18`.

While this is an edge case that may seem negligible, it shouldn't be possible for the user to stake his LP tokens and not earn anything in return.

Additionally, each point is actually [worth `200 * 1e18` BYTES](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L203), so not being able to earn below 4 points here might be unexpected.

Consider adding an `assert` in `_stakeLP` checking `points > 0`.

## 1.3. Using `transferFrom` on ERC721 tokens

The `transferFrom` function is used instead of `safeTransferFrom` here, and [it's discouraged by OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/109778c17c7020618ea4e035efb9f0f9b82d43ca/contracts/token/ERC721/IERC721.sol#L84). We can even find this warning at multiple places in the contest repo here:

```solidity
contracts/s1/beckLoot.sol:
   90:      * WARNING: Usage of this method is discouraged, use {safeTransferFrom} whenever possible.

contracts/s1/NTCitizenDeploy.sol:
  113:      * WARNING: Usage of this method is discouraged, use {safeTransferFrom} whenever possible.

contracts/s1/NTItems.sol:
   93:      * WARNING: Usage of this method is discouraged, use {safeTransferFrom} whenever possible.

contracts/s1/NTLandDeploy.sol:
   93:      * WARNING: Usage of this method is discouraged, use {safeTransferFrom} whenever possible.

contracts/s1/vaultBox.sol:
   90:      * WARNING: Usage of this method is discouraged, use {safeTransferFrom} whenever possible.

contracts/s2/NTOuterCitizenDeploy.sol:
  109:      * WARNING: Usage of this method is discouraged, use {safeTransferFrom} whenever possible.

contracts/s2/NTOuterIdentity.sol:
   94:      * WARNING: Usage of this method is discouraged, use {safeTransferFrom} whenever possible.

contracts/s2/NTS2Items.sol:
   93:      * WARNING: Usage of this method is discouraged, use {safeTransferFrom} whenever possible.

contracts/s2/NTS2LandDeploy.sol:
   93:      * WARNING: Usage of this method is discouraged, use {safeTransferFrom} whenever possible.
```

If the arbitrary address is a contract and is not aware of the incoming ERC721 token, the sent token could be locked.

Consider transitioning from `transferFrom` to `safeTransferFrom` here:

```solidity
NeoTokyoStaker.sol:1498:  _assetTransferFrom(S1_CITIZEN, address(this), msg.sender, citizenId);
NeoTokyoStaker.sol:1563:  _assetTransferFrom(S2_CITIZEN, address(this), msg.sender, citizenId);

NeoTokyoStaker.sol:
  1489:    _assetTransferFrom(
  1490      VAULT,
  1491      address(this),
  1492      msg.sender,
  1493      stakedCitizen.stakedVaultId
  1494     );
  1495    }
```

Notice that [the following](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L191) will need to be replaced in the new method by `safeTransferFrom`'s signature. :

```diff
 /// The `transferFrom` selector for ERC-20 and ERC-721 tokens.
 bytes4 constant private _TRANSFER_FROM_SELECTOR = 0x23b872dd;
+ /// The `safeTransferFrom` selector for ERC-721 tokens.
+ bytes4 constant private _SAFE_TRANSFER_FROM_SELECTOR = 0x42842e0e;
```

## 1.4. `_assetTransferFrom` is used both for ERC721 and ERC20

Just like mentioned in the comment [here](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L190-L191), the `transferFrom` selector is used both for ERC-20 and ERC-721 tokens:

```solidity
 /// The `transferFrom` selector for ERC-20 and ERC-721 tokens.
 bytes4 constant private _TRANSFER_FROM_SELECTOR = 0x23b872dd;
```

While the signatures match by chance (one uses `tokenId`, one uses `amount`), this is actually confusing and it'd be better to separate concerns and follow the previous finding's suggestion of using `safeTransferFrom` to transfer `ERC721` tokens. However, this time, replacing those occurrences will require that `NeoTokyoStaker` inherits from `ERC721TokenReceiver`:

```diff
- contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
+ contract NeoTokyoStaker is PermitControl, ReentrancyGuard, ERC721TokenReceiver {
```

Affected code:

```solidity
contracts/staking/NeoTokyoStaker.sol:
   898:   _assetTransferFrom(S1_CITIZEN, msg.sender, address(this), citizenId);

   928:    _assetTransferFrom(VAULT, msg.sender, address(this), vaultId);

  1011:   _assetTransferFrom(S2_CITIZEN, msg.sender, address(this), citizenId);
```

Hence, `_TRANSFER_FROM_SELECTOR` will only be used for ERC20 tokens and the previously suggested `_SAFE_TRANSFER_FROM_SELECTOR` will be used only for ERC721 tokens.

## 1.5. Known vulnerabilities exist in currently used `@openzeppelin/contracts` version

As some [known vulnerabilities](https://snyk.io/test/npm/@openzeppelin/contracts/4.3.1) exist in the current `@openzeppelin/contracts` version, consider updating `package.json` with at least `@openzeppelin/contracts@4.7.3` here: <https://github.com/code-423n4/2023-03-neotokyo/blob/main/package.json#L23>

The only vulnerability that seems to affect this project is the following:

- [Information Exposure](https://security.snyk.io/vuln/SNYK-JS-OPENZEPPELINCONTRACTS-2958047)

## 1.6. No event emitted when updating a state variable

The configuration methods in `NeoTokyoStaker` don't emit events:

- configureLP
- lockLP
- configureTimelockOptions
- configureIdentityCreditYields
- configureIdentityCreditPoints
- configureVaultCreditMultipliers
- configurePools
- configureCaps

The change methods in `BYTES2` don't emit events:

- changeStakingContractAddress
- changeTreasuryContractAddress

Not emitting events when changing state variables is a bad practice and can be seen as a trust issue

## 1.7. `NeoTokyoStaker.getStakerPosition()` is clunky

It's only possible to fetch the S1 Citizen or S2 Citizen types through [getStakerPosition()](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L689-L700), otherwise the documentation asks to use the [public variables for LP assets](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L676-L677) and the [full output of getStakerPositions()](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L678-L679) for BYTES.

However, this doesn't facilitate integration with external contracts at all. I suggest adding 2 `else if` clauses for the remaining asset types to read the publicly available `stakerLPPosition` mapping and to read BYTES through `getStakerPositions`.

# 2. Non-Critical Issues

## 2.1. `BYTES2.getReward()`'s naming is confusing

Usually, the `get` prefix is used for view-only functions. However, here, this function is actually more of a `claimReward` function, as it calls an external `claimReward` and then mints tokens.

## 2.2. Avoid using magic numbers instead of the existing enum's values

The following is using a hardcoded `2` instead of the clearer, more readable and more maintainable `_assetType == AssetType.BYTES` enum value.

Consider the following:

- [NeoTokyoStaker.sol#L1668](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668)

```diff
- 1668:   if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
+ 1668:   if (_assetType == AssetType.BYTES || uint8(_assetType) > 4) {
```

## 2.3. `"@openzeppelin/contracts"` should be a dependency, not a dev-dependency

```diff
File: package.json
07:   "dependencies": {
08:     "@nomiclabs/hardhat-waffle": "^2.0.3",
09:     "@openzeppelin/contracts-upgradeable": "^4.4.2",
+ 10:     "@openzeppelin/contracts": "^4.3.1",
10:     "chai": "^4.3.6",
11:     "dotenv": "^16.0.1",
12:     "solc": "^0.8.17"
13:   },
14:   "devDependencies": {
15:     "@babel/core": "^7.11.6",
16:     "@babel/plugin-transform-runtime": "^7.11.5",
17:     "@babel/preset-env": "^7.11.5",
18:     "@babel/register": "^7.11.5",
19:     "@babel/runtime": "^7.11.2",
20:     "@nomicfoundation/hardhat-foundry": "^1.0.0",
21:     "@nomiclabs/hardhat-ethers": "^2.0.0",
22:     "@nomiclabs/hardhat-etherscan": "^2.1.1",
23:     "@nomiclabs/hardhat-ganache": "^2.0.0",
- 24:     "@openzeppelin/contracts": "^4.3.1",
```

## 2.4. Duplicated code

The [following code](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L637-L641) is a copy-paste of the [getConfiguredVaultMultiplier()](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L659-L670) method. Consider calling the existing function instead of having a duplicated piece of code.

## 2.5. The `unchecked` statements are undocumented

While there doesn't seem to be an issue with them, there should never exist any doubt in the reason to use an `unchecked` statement in the code.
This will also be beneficial for your future bug bounty program.
Therefore, they should be thorougly documented, but this is never the case:

```solidity
contracts/staking/BYTES2.sol:
  151    /*
  152     We are aware that this math does not round perfectly for all values of
  153     `_amount`. We don't care.
  154    */
  155    uint256 treasuryShare;
  156:   unchecked {
  157     treasuryShare = _amount * 2 / 3;
  158    }
  159    _mint(TREASURY, treasuryShare);
  160   }

contracts/staking/NeoTokyoStaker.sol:
   966    // Update caller staking information and asset data.
   967    PoolData storage pool = _pools[AssetType.S1_CITIZEN];
   968:   unchecked {
   969     citizenStatus.points =
   970      identityPoints * vaultMultiplier * timelockMultiplier /
   971      _DIVISOR / _DIVISOR; 
   972     citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
   973  
   974     // Record the caller's staked S1 Citizen.
   975     _stakerS1Position[msg.sender].push(citizenId);
   976  
   977     // Update the pool point weights for rewards
   978     pool.totalPoints += citizenStatus.points;

  1020    // Update caller staking information and asset data.
  1021    PoolData storage pool = _pools[AssetType.S2_CITIZEN];
  1022:   unchecked {
  1023     citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
  1024     citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
  1025  
  1026     // Record the caller's staked S2 Citizen.
  1027     _stakerS2Position[msg.sender].push(citizenId);
  1028  
  1029     // Update the pool point weights for rewards
  1030     pool.totalPoints += citizenStatus.points;
  1031    }

  1071     // Validate that the caller actually staked the Citizen.
  1072     if (citizenStatus.timelockEndTime == 0) { 
  1073      revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
  1074     }
  1075  
  1076     PoolData storage pool = _pools[AssetType.S1_CITIZEN]; 
  1077:    unchecked {
  1078      uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
  1079      citizenStatus.stakedBytes += amount;
  1080      citizenStatus.points += bonusPoints;
  1081      pool.totalPoints += bonusPoints;
  1082     }
  1083  
  
  1092     // Validate that the caller actually staked the Citizen.
  1093     if (citizenStatus.timelockEndTime == 0) {
  1094      revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
  1095     }
  1096  
  1097     PoolData storage pool = _pools[AssetType.S2_CITIZEN];
  1098:    unchecked {
  1099      uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
  1100      citizenStatus.stakedBytes += amount;
  1101      citizenStatus.points += bonusPoints;
  1102      pool.totalPoints += bonusPoints;
  1103     }
  1104  
  
  1153    // Update caller staking information and asset data.
  1154    PoolData storage pool = _pools[AssetType.LP];
  1155:   unchecked {
  1156     uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
  1157  
  1158     // Update the caller's LP token stake.
  1159     stakerLPPosition[msg.sender].timelockEndTime =
  1160      block.timestamp + timelockDuration;
  1161     stakerLPPosition[msg.sender].amount += amount;
  1162     stakerLPPosition[msg.sender].points += points;
  1163  
  1164     // Update the pool point weights for rewards.
  1165     pool.totalPoints += points;

  1283:      unchecked {
  1284        points += s1Citizen.points;

  1292:      unchecked {
  1293        points += s2Citizen.points;

  1298:     unchecked {
  1299       points += stakerLPPosition[_recipient].points;

  1331:        unchecked {
  1332          uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;
  1333          totalReward += currentRewardRate * timeSinceReward; 
  1334         }

  1342:        unchecked {
  1343          uint256 timeSinceReward = window.startTime - lastPoolRewardTime;
  1344          totalReward += currentRewardRate * timeSinceReward;
  1345         }
  
  1355:         unchecked {
  1356           uint256 timeSinceReward =
  1357            block.timestamp - lastPoolRewardTime;
  1358           totalReward += currentRewardRate * timeSinceReward;
  1359          }

  1378:      unchecked {
  1379        uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;
  1380        totalReward = window.reward * timeSinceReward;
  1381       }

  1387     // Return final shares.
  1388:    unchecked {
  1389      uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
  1390      uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
  1391      share /= _PRECISION;
  1392      daoShare /= _PRECISION;
  1393      return ((share - daoShare), daoShare);
  1394     }

  1443    // Calculate total reward and tax.
  1444    uint256 totalReward;
  1445    uint256 totalTax;
  1446:   unchecked { 
  1447     totalReward = (s1Reward + s2Reward + lpReward);
  1448     totalTax = (s1Tax + s2Tax + lpTax);
  1449    }

  1520:   unchecked {
  1521     pool.totalPoints -= stakedCitizen.points; 
  1522    }

  1583    // Update caller staking information and asset data.
  1584    PoolData storage pool = _pools[AssetType.S2_CITIZEN];
  1585:   unchecked {
  1586     pool.totalPoints -= stakedCitizen.points;
  1587    }

  1626    // Update caller staking information and asset data.
  1627    PoolData storage pool = _pools[AssetType.LP];
  1628:   unchecked {
  1629     uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
  1630  
  1631     // Update the caller's LP token stake.
  1632     lpPosition.amount -= amount;
  1633     lpPosition.points -= points;
  1634  
  1635     // Update the pool point weights for rewards.
  1636     pool.totalPoints -= points;
  1637    }
  1638  
```

## 2.6. Use `delete` instead of setting every fields to their default value

- <https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517-L1521>

```diff
-  stakedCitizen.stakedBytes = 0;  //@audit-issue confusing as devs need to check element by element, just use delete `stakedS1[msg.sender][citizenId]`;
-  stakedCitizen.timelockEndTime = 0;
-  stakedCitizen.points = 0;
-  stakedCitizen.hasVault = false;
-  stakedCitizen.stakedVaultId = 0;
+  delete stakedS1[msg.sender][citizenId];
```

- <https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1582-L1584>

```diff
-  stakedCitizen.stakedBytes = 0; //@audit-issue confusing as devs need to check element by element, just use delete `stakedS2[msg.sender][citizenId];`;
-  stakedCitizen.timelockEndTime = 0;
-  stakedCitizen.points = 0;
+  delete stakedS2[msg.sender][citizenId];
```

## 2.7. Add commented parameter names

When the return statement is documented but unnamed, consider adding a little comment with the name as such: `Type Location /* name */`.

Affected code:

```diff
contracts/staking/NeoTokyoStaker.sol:
   623:   @return The "Credit Yield" trait value of the component Identity item of 
   624     the S1 Citizen with the token ID of `_citizenId`.
   625   */
   626   function getCreditYield ( 
   627    uint256 _citizenId,
   628    uint256 _vaultId
-   629   ) public view returns (string memory) {  
+   629   ) public view returns (string memory /* Credit Yield */) {

   657:   @return The configured point multiplier for the Vault with token ID of 
   658     `_vaultId`.
   659   */
   660   function getConfiguredVaultMultiplier (
   661    uint256 _vaultId
-   662   ) public view returns (uint256) {
+   662   ) public view returns (uint256 /* Configured Vault Multiplier */) {

   687:   @return The list of token IDs of a particular Citizen type that have been 
   688     staked by `_staker`.
   689   */
   690   function getStakerPosition (
   691    address _staker,
   692    AssetType _assetType
-   693   ) external view returns (uint256[] memory) { 
+   693   ) external view returns (uint256[] memory /* TokenId list */) { 

   709:   @return The position of the `_staker` across all asset types.
   710   */
   711   function getStakerPositions (
   712    address _staker
-   713   ) external view returns (StakerPosition memory) { 
+   713   ) external view returns (StakerPosition memory /* Staker Positions */) { 

   823:   @return Whether or not `_a` and `_b` are equal.
   824   */
   825   function _stringEquals (
   826    string memory _a,
   827    string memory _b
-   828   ) private pure returns (bool) {
+   828   ) private pure returns (bool /* `_a` equals `_b` */) {

  1406:   @return A tuple containing (the number of tokens due to be minted to 
  1407     `_recipient` as a reward, and the number of tokens that should be minted 
  1408     to the DAO treasury as a DAO tax).
  1409   */
  1410   function claimReward (
  1411    address _recipient  
-  1412   ) external returns (uint256, uint256) { 
+  1412   ) external returns (uint256 /* Reward amount */, uint256 /* DAO Tax amount */) { 
```

## 2.8. Import declarations should import specific identifiers, rather than the whole file

Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation

```solidity
BYTES2.sol:4:import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
BYTES2.sol:5:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
BYTES2.sol:8:import "../access/PermitControl.sol";
BYTES2.sol:9:import "../interfaces/IByteContract.sol";
BYTES2.sol:10:import "../interfaces/IStaker.sol";
NeoTokyoStaker.sol:4:import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
NeoTokyoStaker.sol:6:import "../access/PermitControl.sol";
NeoTokyoStaker.sol:7:import "../interfaces/IByteContract.sol";
NeoTokyoStaker.sol:8:import "../interfaces/IGenericGetter.sol";
```

## 2.9. Function ordering does not follow the Solidity style guide

According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order: `constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but `NeoTokyoStaker` doesn't follow this pattern
