# 1. Insufficient Input Sanitization
### Files:
1. BYTES2.sol - [L81-L84](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L81)
2. NeoTokyoStaker.sol - [L81-L84](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L81)
### Description:
The BYTES2 contract constructor does not sanitize the input for all arguments as follows:
```
    address _bytes,
    address _s1Citizen,
    address _staker,
    address _treasury
```
The NeoTokyoStaker contract constructor does not sanitize the input for all arguments as follows:
```
    address _bytes,
    address _s1Citizen,
    address _s2Citizen,
    address _lpToken,
    address _identity,
    address _vault,
    uint256 _vaultCap,
    uint256 _noVaultCap
```
# 2. Off-By-One-Bug
### Files:
1. BYTES2.sol - [L81-L84](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L81)
2. NeoTokyoStaker.sol - [L81-L84](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L81)
### Description:
The NeoTokyoStaker contract defines the AssetType enum with four types as follows:
```
    enum AssetType {
        S1_CITIZEN,
        S2_CITIZEN,
        BYTES,
        LP
    }
    function stake(
        AssetType _assetType,
        ...
    // Validate that the asset being staked is of a valid type.
    if (uint8(_assetType) > 4) {
        revert InvalidAssetType(uint256(_assetType));
    }
```
However, the stake function places an improper check for the `AssetType` argument which allows to bypass the check as there does not exist an `AssetType` with 4th index of the enum. Enums in Solidity start with `0` hence a 4th index would be represented by id `3` instead of `4`.
### Recommendation:
Modify the check to ensure only the valid `AssetType`, as defined in the enum, are accepted during the `stake` function.
```
    // Validate that the asset being staked is of a valid type.
    if (uint8(_assetType) > 3) {
        revert InvalidAssetType(uint256(_assetType));
    }
```
# 3. NonStandard Order of Layout
## Description:
As stated in the Solidity style guide, the contract components should be grouped according to their order and visibility;
```
- Outside Contract
  - License statement
  - Pragma statement
  - Import statement
  - Interfaces
  - Libraries
  - Contract statement
- Inside Contract
  - State Variables
  - Events
  - Modifiers
  - Arrays
  - Structs
  - Enums
  - Mappings
  - Constructor
  - Functions
```
### Order of Functions:
Functions should be grouped according to their visibility and ordered:
```
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
```
Within a grouping, place the `view` and `pure` functions last.
* Specifically, for the NeoTokyoStaker contract all `external functions` should be grouped to the top of contract below the constructor such that the calls to the contract find the called `methodId` in the contract as early as possible to match the one in calldata.