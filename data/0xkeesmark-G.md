# [G-01]Cache length in for loops
## Lines of code
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L717  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L734  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1288  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1312  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1499  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1564  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1742  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1765  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1806  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1822  

## Proof of Concept
The overheads outlined below are per loop excluding the first loop
* storage arrays incur 100 gas
* memory arrays use 3 gas
* calldata arrays use 3 gas

Caching the length changes each of these to an array (3 gas). 

## Recommended Mitigation Steps
example of code.
```
- for (uint256 i; i < _inputs.length; ) {
+ uint len = _inputs.length;
+ for (uint256 i; i < len; ) {
```

# [G-02]++i costs less gas than i++
## Lines of code
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L728  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L743  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1284  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1293 
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1369  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1383  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1509  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1574  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1838  

## Proof of Concept
Saves 6 gas per loop.


## Recommended Mitigation Steps
```
- i++
+ ++i
```

# [G-03] Use nested if and, avoid multiple check combinations
## Lines of code
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L910  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L917  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L926  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1834  
## Proof of Concept
Using nested is cheaper than using && multiple check combinations.
Also some of code is easier to read code and unreadable code.
## Recommended Mitigation Steps
example of code.  
```
- if (citizenVaultId != 0 && vaultId != 0) {
+ if (citizenVaultId != 0) {
+    if (vaultId != 0) {
      revert CitizenAlreadyHasVault(citizenVaultId, vaultId);
+    }
```

# [G-04] Using > 0 costs more gas than != 0
## Lines of code
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1477  
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1552  

## Proof of Concept
Using > 0 costs more gas than != 0 on the if statement. Those variables are the type of uint, so do not need to care under 0. 

## Recommended Mitigation Steps
```
- if (stakedCitizen.stakedBytes > 0) {
+ if (stakedCitizen.stakedBytes != 0) {
    _assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
	}
```