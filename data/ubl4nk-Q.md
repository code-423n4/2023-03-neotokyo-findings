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
---------------------------------------------------------------------
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
