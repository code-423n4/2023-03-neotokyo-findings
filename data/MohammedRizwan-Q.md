## Summary

### Low risk issues
| |Issue|Instances| |
|-|:-|:-:|:-:| 
| [L&#x2011;01] | Constructor lacks address(0) checks | 10 |

### Non-critical issues
| |Issue|Instances| |
|-|:-|:-:|:-:| 
| [N&#x2011;01] | address(0) checks are missing | 3 |
| [N&#x2011;02] | Zero value checks are missing | 4 |
| [N&#x2011;03] | Do not use floating solidity compiler version. Lock the solidity compiler version | 2 |

### [L&#x2011;01]  Constructor lacks address(0) checks
Zero-address check should be used to avoid in the constructors, to avoid the risk of setting a storage variable as address(0) at the time of deployment. 
In BYTES2.sol contract, BYTES1 and S1_CITIZEN are immutable therefore any address error in constructor will result in redeployment of contracts.
There are 10 instances of this issue.

```solidity
File: contracts/staking/BYTES2.sol

75	constructor (
76		address _bytes,
77		address _s1Citizen,
78		address _staker,
79		address _treasury
80	) {
81		BYTES1 = _bytes;
82		S1_CITIZEN = _s1Citizen;
83		STAKER = _staker;
84		TREASURY = _treasury;
85	}
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L75-L85)

### Recommended Mitigation Steps:
```solidity
	constructor (
		address _bytes,
		address _s1Citizen,
		address _staker,
		address _treasury
	) {
+                require(_bytes != address(0), "Address can not be zero address.");
+                require(_s1Citizen != address(0), "Address can not be zero address.");
+                require(_staker != address(0), "Address can not be zero address.");
+                require(_treasury != address(0), "Address can not be zero address.");

		BYTES1 = _bytes;
		S1_CITIZEN = _s1Citizen;
		STAKER = _staker;
		TREASURY = _treasury;
	}
```
In NeoTokyoStaker.sol contract, BYTES, S1_CITIZEN, S2_CITIZEN, IDENTITY, VAULT are immutable therefore any address error in constructor will result in redeployment of contracts. Therefore address(0) check is recommended. 

```solidity
File: contracts/staking/NeoTokyoStaker.sol

588	constructor (
589		address _bytes,
590		address _s1Citizen,
591		address _s2Citizen,
592		address _lpToken,
593		address _identity,
594		address _vault,
595		uint256 _vaultCap,
596		uint256 _noVaultCap
597	) {
598		BYTES = _bytes;
599		S1_CITIZEN = _s1Citizen;
600		S2_CITIZEN = _s2Citizen;
601		LP = _lpToken;
602		IDENTITY = _identity;
603		VAULT = _vault;
604		VAULT_CAP = _vaultCap;
605		NO_VAULT_CAP = _noVaultCap;
606	}
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588-L606)

### Recommended Mitigation Steps:
```solidity
	constructor (
		address _bytes,
		address _s1Citizen,
		address _s2Citizen,
		address _lpToken,
		address _identity,
		address _vault,
		uint256 _vaultCap,
		uint256 _noVaultCap
	) {
+                require(_bytes != address(0), "Address can not be zero address.");
+                require(_s1Citizen != address(0), "Address can not be zero address.");
+                require(_s2Citizen != address(0), "Address can not be zero address.");
+                require(_lpToken != address(0), "Address can not be zero address.");
+                require(_identity != address(0), "Address can not be zero address.");
+                require(_vault != address(0), "Address can not be zero address.");

		BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
		LP = _lpToken;
		IDENTITY = _identity;
		VAULT = _vault;
		VAULT_CAP = _vaultCap;
		NO_VAULT_CAP = _noVaultCap;
	}
```

### Non-critical issues
### [N&#x2011;01]   address(0) checks are missing
address can not be zero address. address(0) checks should be used to prevent unnecessary wastage of gas while setting the addresses.
There are 3 instances of this issue.

```solidity
File: contracts/staking/BYTES2.sol

162	function changeStakingContractAddress (
163		address _staker
164	) hasValidPermit(UNIVERSAL, ADMIN) external {
165		STAKER = _staker;
166	}
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L162-L166)

```solidity
File: contracts/staking/BYTES2.sol

173	function changeTreasuryContractAddress (
174		address _treasury
175	) hasValidPermit(UNIVERSAL, ADMIN) external {
176		TREASURY = _treasury;
177	}
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L173-L177)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1708	function configureLP (
1709		address _lp
1710	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
1711		if (lpLocked) {
1712			revert LockedConfigurationOfLP();
1713		}
1714		LP = _lp;
1715	}
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1708-L1715)

### [N&#x2011;02]   Zero value checks are missing
The amount value(uint) can not be zero, a zero value check is recommended to avoid unncessary wastage of gas by end user.
There are 4 instances of this issue.

```solidity
File: contracts/staking/BYTES2.sol

93	function upgradeBytes (
94		uint256 _amount
95	) external {
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L93-L95)

```solidity
File: contracts/staking/BYTES2.sol

140	function burn (
141		address _from,
142		uint256 _amount
143	) hasValidPermit(UNIVERSAL, BURN) external {
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L140-L143)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1851	function configureCaps (
1852		uint256 _vaultedCap,
1853		uint256 _unvaultedCap
1854	) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external {
1855		VAULT_CAP = _vaultedCap;
1856		NO_VAULT_CAP = _unvaultedCap;
1857	}
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1851-L1857)

### [N&#x2011;03]   Do not use floating solidity compiler version. Lock the solidity compiler version
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
There are 2 instances of this issue.

```solidity
File: contracts/staking/BYTES2.sol

2      pragma solidity ^0.8.19;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L2)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

2      pragma solidity ^0.8.19;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L2)
