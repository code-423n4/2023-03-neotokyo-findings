# Number 1
According to the definition of ```AssetType``` in ```NeoTokyoStaker.sol```:
 (line https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L266)
```
enum AssetType {
       S1_CITIZEN, <--- 0
       S2_CITIZEN, <--- 1
       BYTES,      <--- 2
       LP          <--- 3
}
```
There is one mistake while checking the type of asset (line https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205)
(line https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668)
```
if (uint8(_assetType) > 4) {
	revert InvalidAssetType(uint256(_assetType));
}
```
The asset type can not be 4, our maximum _assetType number can be 3 not 4.
The code can be modified as follows:

```
if (uint8(_assetType) > 3) {
	revert InvalidAssetType(uint256(_assetType));
}
```

OR

```
if (uint8(_assetType) >= 4) {
	revert InvalidAssetType(uint256(_assetType));
}
```

# Number 2
Check: ```divide-before-multiply```
Severity: ```Medium```
Confidence: ```Medium```
Description: Solidity's integer division truncates. Thus, performing division before multiplication can lead to precision loss.
Recommendation: Consider ordering multiplication before division.

line : https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1155
```
NeoTokyoStaker._stakeLP(uint256) (contracts/staking/NeoTokyoStaker.sol#1124-1174) performs a multiplication on the result of a division:
	- points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR (contracts/staking/NeoTokyoStaker.sol#1155)
```

line : https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1388
```
NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address) (contracts/staking/NeoTokyoStaker.sol#1264-1396) performs a multiplication on the result of a division:
	- share = points * _PRECISION / pool.totalPoints * totalReward (contracts/staking/NeoTokyoStaker.sol#1388)
```

line : https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1623 
```
NeoTokyoStaker._withdrawLP() (contracts/staking/NeoTokyoStaker.sol#1597-1644) performs a multiplication on the result of a division:
	- points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR (contracts/staking/NeoTokyoStaker.sol#1623)
```
# Number 3
```Pragma version^0.8.19 (contracts/staking/BYTES2.sol#2) necessitates a version too recent to be trusted. Consider deploying with 0.6.12/0.7.6/0.8.16```
```Pragma version^0.8.19 (contracts/staking/NeoTokyoStaker.sol#2) necessitates a version too recent to be trusted. Consider deploying with 0.6.12/0.7.6/0.8.16```
```solc-0.8.19 is not recommended for deployment```
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#incorrect-versions-of-solidity

# Number 4
```

Parameter BYTES2.upgradeBytes(uint256)._amount (contracts/staking/BYTES2.sol#94) is not in mixedCase
Parameter BYTES2.getReward(address)._to (contracts/staking/BYTES2.sol#115) is not in mixedCase
Parameter BYTES2.burn(address,uint256)._from (contracts/staking/BYTES2.sol#141) is not in mixedCase
Parameter BYTES2.burn(address,uint256)._amount (contracts/staking/BYTES2.sol#142) is not in mixedCase
Parameter BYTES2.changeStakingContractAddress(address)._staker (contracts/staking/BYTES2.sol#163) is not in mixedCase
Parameter BYTES2.changeTreasuryContractAddress(address)._treasury (contracts/staking/BYTES2.sol#174) is not in mixedCase
Variable BYTES2.BYTES1 (contracts/staking/BYTES2.sol#43) is not in mixedCase
Variable BYTES2.S1_CITIZEN (contracts/staking/BYTES2.sol#46) is not in mixedCase
Variable BYTES2.STAKER (contracts/staking/BYTES2.sol#49) is not in mixedCase
Variable BYTES2.TREASURY (contracts/staking/BYTES2.sol#52) is not in mixedCase
Parameter NeoTokyoStaker.getCreditYield(uint256,uint256)._citizenId (contracts/staking/NeoTokyoStaker.sol#626) is not in mixedCase
Parameter NeoTokyoStaker.getCreditYield(uint256,uint256)._vaultId (contracts/staking/NeoTokyoStaker.sol#627) is not in mixedCase
Parameter NeoTokyoStaker.getConfiguredVaultMultiplier(uint256)._vaultId (contracts/staking/NeoTokyoStaker.sol#660) is not in mixedCase
Parameter NeoTokyoStaker.getStakerPosition(address,NeoTokyoStaker.AssetType)._staker (contracts/staking/NeoTokyoStaker.sol#690) is not in mixedCase
Parameter NeoTokyoStaker.getStakerPosition(address,NeoTokyoStaker.AssetType)._assetType (contracts/staking/NeoTokyoStaker.sol#691) is not in mixedCase
Parameter NeoTokyoStaker.getStakerPositions(address)._staker (contracts/staking/NeoTokyoStaker.sol#711) is not in mixedCase
Parameter NeoTokyoStaker.stake(NeoTokyoStaker.AssetType,uint256,uint256,uint256,uint256)._assetType (contracts/staking/NeoTokyoStaker.sol#1197) is not in mixedCase
Parameter NeoTokyoStaker.stake(NeoTokyoStaker.AssetType,uint256,uint256,uint256,uint256)._timelockId (contracts/staking/NeoTokyoStaker.sol#1198) is not in mixedCase
Parameter NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address)._assetType (contracts/staking/NeoTokyoStaker.sol#1265) is not in mixedCase
Parameter NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address)._recipient (contracts/staking/NeoTokyoStaker.sol#1266) is not in mixedCase
Parameter NeoTokyoStaker.claimReward(address)._recipient (contracts/staking/NeoTokyoStaker.sol#1410) is not in mixedCase
Parameter NeoTokyoStaker.withdraw(NeoTokyoStaker.AssetType,uint256)._assetType (contracts/staking/NeoTokyoStaker.sol#1660) is not in mixedCase
Parameter NeoTokyoStaker.configureLP(address)._lp (contracts/staking/NeoTokyoStaker.sol#1709) is not in mixedCase
Parameter NeoTokyoStaker.configureTimelockOptions(NeoTokyoStaker.AssetType,uint256[],uint256[])._assetType (contracts/staking/NeoTokyoStaker.sol#1738) is not in mixedCase
Parameter NeoTokyoStaker.configureTimelockOptions(NeoTokyoStaker.AssetType,uint256[],uint256[])._timelockIds (contracts/staking/NeoTokyoStaker.sol#1739) is not in mixedCase
Parameter NeoTokyoStaker.configureTimelockOptions(NeoTokyoStaker.AssetType,uint256[],uint256[])._encodedSettings (contracts/staking/NeoTokyoStaker.sol#1740) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditYields(uint256[],string[],string[])._citizenRewardRates (contracts/staking/NeoTokyoStaker.sol#1761) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditYields(uint256[],string[],string[])._vaultRewardRates (contracts/staking/NeoTokyoStaker.sol#1762) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditYields(uint256[],string[],string[])._identityCreditYields (contracts/staking/NeoTokyoStaker.sol#1763) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditPoints(string[],uint256[])._identityCreditYields (contracts/staking/NeoTokyoStaker.sol#1784) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditPoints(string[],uint256[])._points (contracts/staking/NeoTokyoStaker.sol#1785) is not in mixedCase
Parameter NeoTokyoStaker.configureVaultCreditMultipliers(string[],uint256[])._vaultCreditMultipliers (contracts/staking/NeoTokyoStaker.sol#1803) is not in mixedCase
Parameter NeoTokyoStaker.configureVaultCreditMultipliers(string[],uint256[])._multipliers (contracts/staking/NeoTokyoStaker.sol#1804) is not in mixedCase
Parameter NeoTokyoStaker.configurePools(NeoTokyoStaker.PoolConfigurationInput[])._inputs (contracts/staking/NeoTokyoStaker.sol#1820) is not in mixedCase
Parameter NeoTokyoStaker.configureCaps(uint256,uint256)._vaultedCap (contracts/staking/NeoTokyoStaker.sol#1852) is not in mixedCase
Parameter NeoTokyoStaker.configureCaps(uint256,uint256)._unvaultedCap (contracts/staking/NeoTokyoStaker.sol#1853) is not in mixedCase
Variable NeoTokyoStaker.BYTES (contracts/staking/NeoTokyoStaker.sol#223) is not in mixedCase
Variable NeoTokyoStaker.S1_CITIZEN (contracts/staking/NeoTokyoStaker.sol#226) is not in mixedCase
Variable NeoTokyoStaker.S2_CITIZEN (contracts/staking/NeoTokyoStaker.sol#229) is not in mixedCase
Variable NeoTokyoStaker.LP (contracts/staking/NeoTokyoStaker.sol#232) is not in mixedCase
Variable NeoTokyoStaker.IDENTITY (contracts/staking/NeoTokyoStaker.sol#239) is not in mixedCase
Variable NeoTokyoStaker.VAULT (contracts/staking/NeoTokyoStaker.sol#242) is not in mixedCase
Variable NeoTokyoStaker.VAULT_CAP (contracts/staking/NeoTokyoStaker.sol#249) is not in mixedCase
Variable NeoTokyoStaker.NO_VAULT_CAP (contracts/staking/NeoTokyoStaker.sol#255) is not in mixedCase
```
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#conformance-to-solidity-naming-conventions