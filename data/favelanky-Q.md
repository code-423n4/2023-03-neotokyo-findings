## [L-1] Floating pragma

There are 2 instance of this issue:

Both contracts have floating pragma.

```solidity
pragma solidity ^0.8.19;
```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol


https://swcregistry.io/docs/SWC-103

Recommendation: using fixed solidity version.

## [L-2] Division before multiplication

There are 2 instance of this issue:

It is not good to divide before multiplying. Values can be truncated.

```
uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388
## [L-3] Validate input parameters

There is 2 instance of this issue:

Validate that:
- `_timelockIds.length` == `_encodedSettings.length`
- Each `uint256` in the `_encodedSettings` is properly encoded. First 128 bits unequal 0 and last 128 bits unequal zero.

```solidity
    function configureTimelockOptions(
        AssetType _assetType,
        uint256[] memory _timelockIds,
        uint256[] memory _encodedSettings
    ) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
        for (uint256 i; i < _timelockIds.length; ) {
            timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];
            unchecked {
                ++i;
            }
        }
    }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737

## [NC-1] Pragma version^0.8.19 version too recent to be trusted.

There are 2 instance of this issue:

0.8.19 (2023-02-22)
0.8.18 (2023-02-01)
0.8.17 (2022-09-08)  
0.8.16 (2022-08-08)  
0.8.15 (2022-06-15) 

- Unexpected bugs can be reported in recent versions;  
- Risks related to recent releases  
- Risks of complex code generation changes  
- Risks of new language features  
- Risks of known bugs

Recommendation: Use more battle-tested version.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

## [NC-2] Order of filling structure

There is 1 instance of this issue:

It is more reasonable to fill the structure according to its order.

```solidity
stakedS1Details[i] = StakedS1CitizenOutput({
                citizenId: citizenId,
                stakedBytes: citizenDetails.stakedBytes,
                timelockEndTime: citizenDetails.timelockEndTime,
                points: citizenDetails.points,
                hasVault: citizenDetails.hasVault, // swap `hasVault` with `stakedVaultId`
                stakedVaultId: citizenDetails.stakedVaultId
            });
```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L720-L727

## [NC-3] Change functions naming

There are 2 instance of this issue:

Since these functions revert, it's more reasonable to rename them to `_assetSafeTransferFrom` and `_assetTransfer`.

```solidity
	function _assetTransferFrom(address _asset, address _from, address _to, uint256 _idOrAmount) private {

    function _assetTransfer(address _asset, address _to, uint256 _amount) private {
```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L796
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L766

## [NC-4] Unnecessary division

There is 1 instance of this issue:

`_DIVISOR` is a constant value equal 100. It makes no sense to multiply and divide by the same number. It will also save some gas.

```solidity
citizenStatus.points = (100 * timelockMultiplier) / _DIVISOR;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1022
