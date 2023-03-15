# Gas optimization in deployment of NeoTokyoStaker contract

Currently there is repeated logic between the `getCreditYield` and `getConfiguredVaultMultiplier` methods.  The following block of code exists in both functions ([here](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L638) and [here](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L664)):

```
IGenericGetter vault = IGenericGetter(VAULT);
string memory vaultMultiplier = (_vaultId != 0)
	? vault.getCreditMultiplier(_vaultId)
	: "";
```

Deployment gas costs can be reduced by moving this logic into one new method, like so:

```
function getVaultMultiplier(
	uint256 _vaultId
) private view returns (string memory) {
	IGenericGetter vault = IGenericGetter(VAULT);
	return (_vaultId != 0)
		? vault.getCreditMultiplier(_vaultId)
		: "";
}
```

Then the `getCreditYield` and `getConfiguredVaultMultiplier` methods can be modified as follows to call the `getVaultMultiplier` method:

```
function getCreditYield (
	uint256 _citizenId,
	uint256 _vaultId
) public view returns (string memory) {

	// Retrieve the total reward rate of this S1 Citizen.
	IGenericGetter citizen = IGenericGetter(S1_CITIZEN);
	uint256 rewardRate = citizen.getRewardRateOfTokenId(_citizenId);
	if (rewardRate == 0) {
		revert CitizenDoesNotExist(_citizenId);
	}
	
	// Deduce the original Identity credit yield.
	return identityCreditYield[rewardRate][getVaultMultiplier(_vaultId)];
}
```

And:

```
function getConfiguredVaultMultiplier (
	uint256 _vaultId
) public view returns (uint256) {		
	// Deduce the configured Vault multiplier.
	return vaultCreditMultiplier[getVaultMultiplier(_vaultId)];
}
```

After this change, the deployment gas costs of the NeoTokyoStaker contract are improved as follows:

Before this change:
```
···················|···································|·············|·············|·············|···············|··············
|  Deployments                                         ·                                         ·  % of limit   ·             │
·······················································|·············|·············|·············|···············|··············
|  NeoTokyoStaker                                      ·          -  ·          -  ·    4019932  ·       13.4 %  ·          -  │
·------------------------------------------------------|-------------|-------------|-------------|---------------|-------------·
```

After this change:

```
···················|···································|·············|·············|·············|···············|··············
|  Deployments                                         ·                                         ·  % of limit   ·             │
·······················································|·············|·············|·············|···············|··············
|  NeoTokyoStaker                                      ·          -  ·          -  ·    3994296  ·       13.3 %  ·          -  │
·------------------------------------------------------|-------------|-------------|-------------|---------------|-------------·
```

Thus we see an average contract deployment improvement of 25,636 gas.

# Gas optimization in `burn` method and deployment of BYTES2 contract

The `burn` method can be made more efficient by removing the unneeded `treasuryShare` variable and simply making the call to the `_mint` method directly inside the `unchecked` code block like so:

```
function burn (
	address _from,
	uint256 _amount
) hasValidPermit(UNIVERSAL, BURN) external {
	_burn(_from, _amount);

	/*
		We are aware that this math does not round perfectly for all values of
		`_amount`. We don't care.
	*/
	// uint256 treasuryShare; /* REMOVED */
	unchecked {
		// treasuryShare = _amount * 2 / 3; /* REMOVED */
		_mint(TREASURY, _amount * 2 / 3);
	}
	// _mint(TREASURY, treasuryShare); /* REMOVED */
}
```

After this change, the deployment gas costs of the BYTES2 contract are improved as follows:

Before this change:
```
···················|···································|·············|·············|·············|···············|··············
|  Deployments                                         ·                                         ·  % of limit   ·             │
·······················································|·············|·············|·············|···············|··············
|  BYTES2                                              ·          -  ·          -  ·    1389711  ·        4.6 %  ·          -  │
·······················································|·············|·············|·············|···············|··············
```

After this change:
```
···················|···································|·············|·············|·············|···············|··············
|  Deployments                                         ·                                         ·  % of limit   ·             │
·······················································|·············|·············|·············|···············|··············
|  BYTES2                                              ·          -  ·          -  ·    1387743  ·        4.6 %  ·          -  │
·······················································|·············|·············|·············|···············|··············
```

Thus we see an average contract deployment improvement of 1,968 gas. The gas cost of using the `burn` method are also improved.