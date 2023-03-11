QA1: There should be a range check for ``VAULT_CAP`` and ``NO_VAULT_CAP`` check.

[https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588-L606](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588-L606)

[https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1851-L1857](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1851-L1857)

QA2: ``_assetTransferFrom()``'s revert will not return correct error message.

The following code in ``_assetTransferFrom()`` will not return correct revert error message, an experiment can easily show that - it will return some obscure bytecode unreadable message. This is because the first word of ``data`` actually stores the number of bytes in ``data``.

```javascript
if (!success) {
			revert(string(data));
		}
```

The correct way is: 
```
if (!success) {
      if(data.length > 0) {
           let len :=  mload(data)
           revert(add(32, data), len);
      }     
}
```
