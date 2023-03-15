**---- NeoTokyoStaker.sol ----**

Redundant Code because of repetition in the following lines:  
[L638-L641](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L638)
[L663-L667](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L663)
Consider moving that logic into a seperate function to save gas. 
```
function getVaultMultiplier(uint256 _vaultId) internal returns(string memory){
  // Retrieve the credit rate multiplier of the Vault.
  IGenericGetter vault = IGenericGetter(VAULT);
  return (_vaultId != 0) ? vault.getCreditMultiplier(_vaultId) : "";
}
```
Replace [L644](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L644)
`return identityCreditYield[rewardRate][getVaultMultiplier(_vaultId)];`  
Replace [L670](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L670)  
`return vaultCreditMultiplier[getVaultMultiplier(_vaultId)];`  


**---- NeoTokyoStaker.sol ----**
Consider changing `_stringEquals(_a, _b)` in [L824](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L824) with 2107 gas cost for:  
```
/**
 * @dev Returns true if the two strings are equal.
 */
function equal(string memory a, string memory b) internal pure returns (bool) {
  return keccak256(bytes(a)) == keccak256(bytes(b));
}
```
This is implemented by openzeppelin, it's less code, easier to read and more gas efficient 
with 1931 gas of execution cost vs 2107.   
