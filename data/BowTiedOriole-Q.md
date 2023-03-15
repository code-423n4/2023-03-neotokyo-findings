# QA Report

## Table of Contents

- [Low-Risk Findings](#low-risk-findings)
  - [L-01] Value of 4 for AssetType will result in incorrect revert message
  - [L-02] Unnecessary precision loss for reward calculation
  - [L-03] configureIdentityCreditYields allows arrays of different lengths
- [Non-Critical Findings](#non-critical-findings)
  - [NC-01] NatSpec comment for BYTES2 constructor incorrectly says BYTES 2.0 instead of BYTES 1.0
  - [NC-02] Burn call in upgradeBytes will implicitly check the user's balance, do not need to directly check

## Low-Risk Findings

### [L-01] Value of 4 for AssetType will result in incorrect revert message

The AssetType enum has four values corresponding to values 0-3. However, checks for assetType in the code base use the '> 4' qualifier. If the user inputs an assetType of 4, this will result in an incorrect error message.

#### Findings

[NeoTokyoStaker.sol#L1205](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205-L1207)
[NeoTokyoStaker.sol#L1668](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668-L1670)

```solidity
L1205: if (uint8(_assetType) > 4) {
L1206:     revert InvalidAssetType(uint256(_assetType));
L1207: }
```

```solidity
L1668: if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
L1669:     revert InvalidAssetType(uint256(_assetType));
L1700: }
```

#### Recommended mitigation steps

Use > 3 for these checks.

### [L-02] getPoolReward contains unnecessary precision loss for reward calculation

getPoolReward divides by pool.totalPoints prior to multiplying by totalReward. Multiplication should be done first to avoid precision loss.

#### Findings

[NeoTokyoStaker.sol#L1388](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388)

```solidity
uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
```

#### Recommended mitigation steps

```solidity
uint256 share = points * _PRECISION * totalReward / pool.totalPoints;
```

### [L-03] configureIdentityCreditYields allows arrays of different lengths

configureIdentifyCreditYields accepts two arrays as function parameters: _timelockIds and _encodedSettings. There is no check confirming that these arrays are the same length.

#### Findings

[NeoTokyoStaker.sol#L1737-1746](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737-L1746)

```solidity
function configureTimelockOptions (
	AssetType _assetType,
	uint256[] memory _timelockIds,
	uint256[] memory _encodedSettings
) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
	for (uint256 i; i < _timelockIds.length; ) {
		timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];
		unchecked { ++i; }
	}
}
```

#### Recommended mitigation steps

Add require statement to confirm _timelockIds and _encodedSettings are the same length.

```solidity
require(_timelockIds.length == _encodedSettings.length);
```

## Non-Critical Findings

### [NC-01] NatSpec comment for BYTES2 constructor incorrectly says BYTES 2.0 instead of BYTES 1.0

The constructor for BYTES2 accepts the address for the BYTES 1.0 contract. The NatSpec incorrectly says 2.0.

#### Findings

[BYTES2.sol#L70](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L70)

```solidity
@param _bytes The address of the BYTES 2.0 ERC-20 token contract.
```

#### Recommended mitigation steps

Adjust NatSpec to say 'The address of the BYTES 1.0 ERC-20 token contract.'

### [NC-02] Burn call in upgradeBytes will implicitly check the user's balance, do not need to directly check

The function upgradeBytes starts with a call that confirms the user's balance is greater than _amount. This check is not necessary because the subsequent burn call will implicitly check this balance.

#### Findings

[BYTES2.sol#L96-101](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L96-L101)

```solidity
if (IERC20(BYTES1).balanceOf(msg.sender) < _amount) {
	revert DoNotHaveEnoughOldBytes(_amount);
}


// Burn the original BYTES 1.0 tokens and mint replacement BYTES 2.0.
IByteContract(BYTES1).burn(msg.sender, _amount);
```

#### Recommended mitigation steps

Consider removing the first if statement. If the user's balance is less than _amount, the burn call will revert.