### LOW 1

NeoTokyo uses custom-written `PermitControl`:
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/access/PermitControl.sol#L21

It is recommended to use OpenZeppelin `AccessControl` instead of custom-written `PermitControl`. This has several benefits, making it a preferable choice:
1. Security: OpenZeppelin's contracts have been extensively reviewed, tested, and audited by the community and security experts
2. Standardization: OpenZeppelin's libraries and contracts follow industry best practices and standard patterns.
3. Maintainability: OpenZeppelin actively maintains and updates its contracts to address any new issues or changes in the Ethereum ecosystem

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol

### LOW 2

NeoTokyo uses a single general interface `IGenericGetter` to call different contracts even though their ABI is different:  
* https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L631
* https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L638
* https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L664
* https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L903
* https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L949

Using a single `IGenericGetter` interface for all contracts instead of separate interfaces for each contract can be problematic for catching type-related errors.

### LOW 3

There is type-unsafe comparison in `withdraw()`:
```
uint8(_assetType) == 2
``` 
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668

which should be replaced with
```
_assetType == AssetType.BYTES
```

### LOW 4

There is loss of precision in some formulas in the NeoTokyo contract. Generally it is better to put the division operation in the and of the expression.

The line:
```
uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1623

can be replaced with 

```
uint256 points = (amount * 100 * lpPosition.multiplier) / (1e18 * _DIVISOR);
```

The line:
```
uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1155

can be replaced with
```
uint256 points = amount * 100 * timelockMultiplier / (1e18 * _DIVISOR);
```

