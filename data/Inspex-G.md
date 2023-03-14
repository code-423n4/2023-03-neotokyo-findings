## Gas Optimizations

| Number | Issue | Instances | Total Gas Saved |
|---|---|---|---|
| Gas-01 | Constructors can be marked payable | 2 | - |
| Gas-02 | Use of Named Returns for Local Variables Saves Gas | 1 | - |
| Gas-03 | Using storage instead of memory for struct/arrays saves gas | 2 | 4200 |
| Gas-04 | Use assembly to write address storage values | 2 | - |
| Gas-05 | Optimize names to save gas | 2 | - |

In total, there were 18 instances of 5 issues, resulting in a total gas savings of at least 4200 (this represents the minimum amount of gas saved).

### [Gas-01] Constructors can be marked payable

#### Description
Payable functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided. A constructor can safely be marked as payable, since only the deployer would be able to pass funds, and the project itself would not pass any funds.

Proof of concept: https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5?u=pcaversaccio

Instances(2):
```
File: BYTES2.sol

75:	constructor (
```
Link to Code: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol

```
File: NeoTokyoStaker.sol

588:	constructor (
```
Link to Code: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

#### Recommendation
We suggest setting the constructor to `payable`.

### [Gas-02] Use of Named Returns for Local Variables Saves Gas
#### Description
Using named return values as temporary local variables can help you conserve gas.
Instances(1):

```
File: NeoTokyoStaker.sol

1388:		uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
1389:		uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
```

Link to Code: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
#### Recommendation

We suggest using named returns for local variables. 

For example:

```
File: NeoTokyoStaker.sol

1267:	) public view returns (uint256 share, uint256 points) {


1388:		share = points * _PRECISION / pool.totalPoints * totalReward;
1389:		daoShare = share * pool.daoTax / (100 * _DIVISOR);
```

### [Gas-03] Using storage instead of memory for struct/arrays saves gas

#### Description
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instances(2):

```
File: NeoTokyoStaker.sol

1281:	StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];

1290:	StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];
```

Link to Code: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

#### Recommendation

We suggest using storage instead of memory for `struct/arrays` to save gas.

For example:

```
File: NeoTokyoStaker.sol

1281:	StakedS1Citizen storage s1Citizen = stakedS1[_recipient][citizenId];

1290:	StakedS2Citizen storage s2Citizen = stakedS2[_recipient][citizenId];
```

### [Gas-04] Use assembly to write address storage values

#### Description
Using assembly to store an address can save gas because it allows direct access to the EVM, which enables more efficient manipulation of storage.

Instances(13):
```
File: BYTES2.sol

81:		BYTES1 = _bytes;

82:		S1_CITIZEN = _s1Citizen;

83:		STAKER = _staker;

84:		TREASURY = _treasury;

165:		STAKER = _staker;

176:		TREASURY = _treasury;
```
Link to Code: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol

```
File: NeoTokyoStaker.sol

598:		BYTES = _bytes;

599:		S1_CITIZEN = _s1Citizen;

600:		S2_CITIZEN = _s2Citizen;

601:		LP = _lpToken;

602:		IDENTITY = _identity;

603:		VAULT = _vault;

1714:		LP = _lp;
```
Link to Code: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

#### Recommendation
We suggest using assembly to write address storage value to save gas.

For example:
```
File: BYTES2.sol

75:    constructor (
76:        address _bytes,
77:        address _s1Citizen,
78:        address _staker,
79:        address _treasury
80:    ) {
81:        assembly {
82:            sstore(0x0a, _bytes)
83:            sstore(0x0b, _s1Citizen)
84:            sstore(0x08, _staker)
85:            sstore(0x09, _treasury)
86:      }
87:    }
```

### [Gas-05] Optimize names to save gas

#### Description
Each function has a unique Method ID, and for each function, the EVM increases the gas cost by 22 for every function that comes before it in the Method ID order. This means that arranging frequently used functions with lower Method IDs can help reduce gas costs.

Proof of concept: https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

For example:
The following table contains all functions in the `BYTES2` contract sorted by MethodID from lower to higher.

| Method ID | Function |
|-----------|----------|
| 0x195285db| upgradeBytes(uint256) |
| 0x2c8e8dfa | updateReward(address,address,uint256) |
| 0x5b0510bf | changeTreasuryContractAddress(address) |
| 0x9dc29fac | burn(address,uint256) |
| 0xc00007b0| getReward(address) |
| 0xcc240c01 |updateRewardOnMint(address,uint256)|
| 0xefbf00b0 | changeStakingContractAddress(address) |

#### Recommendation

We suggest optimizing the names of frequently called functions with lower Method ID.