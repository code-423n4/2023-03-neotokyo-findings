## QA & Low
### Needless multiplying and dividing
#### Description
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1022

`citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;`
but `_DIVISOR == 100`
#### Recommendation
fix for 

`citizenStatus.points = timelockMultiplier`
### Stake and Claim gas cost (possible out of gas)
 
 #### Description
Functions for staking and claiming rewards are expensive (stake is around 13k), so if holder want to stake 2307 assets he can't stake more (30 000 000 gas). All that operations will be expensive. 

#### Recommendation
paging? or store off-chain some data?
### Useless parameter 
#### Description
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205
4 is impossible parameter
there is no function for it, but it will pass this `require`
#### Recommendation
`if (uint8(_assetType) == 2 || uint8(_assetType) > 3)` (more gas efficient)
or
`if (uint8(_assetType) == 2 || uint8(_assetType) >= 4)`
and 
`if (uint8(_assetType) > 3) {` (more gas efficient)
or
`if (uint8(_assetType) >= 4) {`

### Better names for function parameters
#### Description 
```
		string memory citizenCreditYield = getCreditYield(
			citizenId,
			citizenVaultId
		);
```
```
	function getCreditYield (
		uint256 _citizenId,
		uint256 _vaultId
	) public view returns (string memory) {
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L625
#### Recommendation 
rename names in function as:
```
	function getCreditYield (
		uint256 _citizenId,
		uint256 _citizenVaultId
	) public view returns (string memory) {
```

That function called only once in contracts. 