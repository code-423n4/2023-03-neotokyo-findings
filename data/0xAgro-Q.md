# QA Report
## Finding Summary

[**Low Severity**](#Low-Severity)
1. [**Early Use of Compiler Version**](#1-Early-Use-of-Compiler-Version)
2. [**Rewards Sent on Behalf**](#2-Rewards-Sent-on-Behalf)
3. [**Precision Loss**](#3-Precision-Loss)
4. [**Lack of Function Misuse Protection**](#4-Lack-of-Function-Misuse-Protection)

[**Non-Critical**](#Non-Critical)
1. [**Spelling Mistakes**](#1-Spelling-Mistakes)
2. [**Respect Your Code**](#2-Respect-Your-Code)
3. [**Multivariate Event Parameters**](#3-Multivariate-Event-Parameters)

## Low Severity

## 1. Early Use of Compiler Version

All functions in scope operate under Solidity `0.8.19`. Solidity `0.8.19` as of the start of the contest, is less than [3 weeks old](https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/).

By using a compiler version early you are volunteering as guinea pigs for potential bugs when it is not necessary. As an example in [Solidity 0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/), [a compiler bug](https://github.com/ethereum/solidity-blog/blob/499ab8abc19391be7b7b34f88953a067029a5b45/_posts/2022-06-15-inline-assembly-memory-side-effects-bug.md) was found 81 days after the release announcement (see links).

Consider downgrading contracts of version less `0.8.19` to a more battle-tested Solidity version.

## 2. Rewards Sent on Behalf

The [`getReward`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114) function of [BYTES2.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol) is callable on behalf of any user. There is a [comment](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L109) that states:
>This function is called by the S1 Citizen contract to emit BYTES to callers

If the desire is to have [`getReward`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114) only callable by the S1 Citizen contract, consider adding a check that the caller is the S1 Citizen contract only. 

By having an external rewards function callable on behalf of users, there is a risk of funds being lost through external user contract assumptions. 

## 3. Precision Loss

There are some instances of precision loss from a divide before mulitply:

```Solidity
1155:	uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
1388:	uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
1623:	uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
```

For large inputs, the wrong values are calculated. Consider doing all multiplication before division in all expressions.

## 4. Lack of Function Misuse Protection

The [stake](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196) and [withdraw](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1659) functions use ambiguous calldata that depends on the underlying function being called.

For example [this](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1653-L1654) comment states:
> The third parameter is overloaded to have different meaning depending on the `assetType` selected. 

There is no checks in either the underlying [stake](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196) or [withdraw](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1659) functions that makes sure the user parameters are reasonable inputs. If a user accidently uses the wrong parameters, it can result in unwanted actions. Consider making each action a different function or add input sanitation if possible.

## Non-Critical

## 1. Spelling Mistakes

There are some spelling mistakes throughout the codebase. Spelling mistakes can confuse readers and possibly result in uninformed user decisions. Consider fixing all spelling mistakes. The following were not found in the [automated report](https://gist.github.com/Picodes/01427c59b07c651699136589541159a7#nc-1-typos).

*contracts/staking/NeoTokyoStaker.sol*

* The word `time` is misspelled as [`tiume`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L417).
* The word `citizen` is misspelled as [`ctiizen`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L535).
* The word `configured` is misspelled as [`configued`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L654).
* The word `entirety` is misspelled as [`entireity`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1373).
* The word `function` is misspelled as [`funciton`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1776).

## 2. Respect Your Code

Consider respecting your code:

*/contracts/staking/NeoTokyoStaker.sol*

```Solidity
1335:	// We have no forward goto and thus include this bastardry.
```

## 3. Multivariate Event Parameters

In the [NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol) contract the [`Stake`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L539) and [`Withdraw`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L567) events have a multivariate parameter `amountOrTokenId`. Consider making individual events per staking type.