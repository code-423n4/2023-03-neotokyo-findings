

| S No. | Issue | Instances |
|-----|-----|-----|
| [01] | Event is missing indexed fields | 4
| [02] | Non-library or interface files should use fixed compiler versions, not floating ones | 2
| [03] | Typos | 2
| [04] | LINT | 1

-------------

## 01 Event is missing indexed fields

Each `event` should use three `indexed` fields if there are three or more fields

_There are 4 instances of this issue_

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol

```
File: contracts/staking/BYTES2.sol

61-64: event BytesUpgraded (
		address indexed caller,
		uint256 amount
	);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

```
File: contracts/staking/NeoTokyoStaker.sol

539-544: event Stake (
		address indexed staker,
		address indexed asset,
		uint256 timelockOption,
		uint256 amountOrTokenId
	);
553-557: event Claim (
		address indexed recipient,
		uint256 reward,
		uint256 tax
	);
567-571: event Withdraw (
		address indexed caller,
		address indexed asset,
		uint256 amountOrTokenId
	);
```

-----------

## 02 Non-library or interface files should use fixed compiler versions, not floating ones

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

_There are 2 instances of this issue_

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol

```
File: contracts/staking/BYTES2.sol

2: pragma solidity ^0.8.19;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

```
File: contracts/staking/NeoTokyoStaker.sol

2: pragma solidity ^0.8.19;
```

-----

## 03 Typos

_There are 2 instances of this issue_

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

```
File: contracts/staking/NeoTokyoStaker.sol

495: address in this staker.
619: if there is one. This parameter is separated to optimized for callers who
```

Typo: For line 495, it should be `in this stake` instead of `in this staker
For line 619, it should be `to be optimized` instead of `to optimized`

------------

## 04 LINT

Wrong indentation. Extra spaces are present.

_There is 1 instance of this issue_

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

```
File: contracts/staking/NeoTokyoStaker.sol

181: S2) and BYTES for  time-locked emission rewards. The staker operates on a
```

--------------

