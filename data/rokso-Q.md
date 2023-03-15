# QA report

### **Total Findings: 4**

| Issue Id | Level        | Description                                                                                        | No of findings |
| -------- | ------------ | -------------------------------------------------------------------------------------------------- | -------------- |
| N-01     | Non Critical | [Possible to stake 0 amount of LP](#n-01-possible-to-stake-0-amount-of-lp)                         | 1              |
| N-02     | Non Critical | [Wrong `if` condition](#n-02-wrong-if-condition)                                                   | 2              |
| R-01     | Refactor     | [Unnecessary code logic for string comparison](#r-01-unnecessary-code-logic-for-string-comparison) | 1              |


## [N-01] Possible to stake 0 amount of LP
**Issue:** Stake LP function in neoTokyoStaker allows to stake 0 LP amount

**Recommendation:** Consider adding non zero amount check

**Code:** [NeoTokyoStaker.sol#L1124-L1137](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1124-L1137)
```solidity
    function _stakeLP(uint256 _timelock) private {
        uint256 amount;
        assembly {
            amount := calldataload(0x44)
        }
```

## [N-02] Wrong `if` condition
**Issue:** Found this issues at 2 places. There is wrong `if` condition around AssetType. AssetType hold values in 0-3 range, but `if` condition `uint8(_assetType) > 4` which is wrong.

**Recommendation:** Consider either  just remove `if` condition function param will run this check internally or updating `if` condition with proper check.

**Code:** 
This issue is at 2 place in contract

[NeoTokyoStaker.sol#L1205](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205)
```solidity
1205:		if (uint8(_assetType) > 4) {
1206:			revert InvalidAssetType(uint256(_assetType));
1207:		}
```

[NeoTokyoStaker.sol#L1668](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668)
```solidity
1168:		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
1169:			revert InvalidAssetType(uint256(_assetType));
1170:		}
```

## [R-01] Unnecessary code logic for string comparison
**Issue:** Contract has a huge function to check string equality while there are other easy to use options available.

**Recommendation:** Consider adding check like this.
```solidity
    if (keccak256(abi.encodePacked(_a)) == keccak256(abi.encodePacked(_b)));
```

**Code:** 
[NeoTokyoStaker.sol#L824-L865](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L824-L865)