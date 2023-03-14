Empty function need revert
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1250
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1693

Recommendation: 
To fix this vulnerability, it is recommended to add a default case to the switch statement that throws an error indicating an invalid asset type. Additionally, it is recommended to review the validation checks and ensure they are sufficient to prevent unauthorized access to the pool.
Example:
```
// Select the proper staking function based on the asset type being staked.
function (uint256) _stake;
assembly {
    switch _assetType
        case 0 {
            _stake := _s1
        }
        case 1 {
            _stake := _s2
        }
        case 2 {
            _stake := _b
        }
        case 3 {
            _stake := _lp
        }
        default {
            revert InvalidAssetType(uint256(_assetType));
        }
}
```

