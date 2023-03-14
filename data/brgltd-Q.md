# [01] Unbounded loop

## Impact

Currently, the arrays `_stakerS2Position[_recipient]` and `_stakesS2Position[_recipient]` can grow indefinitely. E.g. there's no maximum limit and there's no functionality to remove items.

If these arrays grows too large, calling `NeoTokyoStaker.claimReward()` might run out of gas and revert. Claiming and rewards will result in a DOS condition.

## Proof of Concept

New items are pushed into `NeoTokyoStaker._stakeS1Citizen()` and `NeoTokyoStaker._stakeS2Citizen()`. 

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L974

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1026

`NeoTokypStaker.claimReward()` will call `NeoTokyoStaker.getPoolReward()` which will iterate all array items.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1279-L1286

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1288-L1295

## Recommendation

Add a functionality to delete items or add a maximum size limit. If this is not possible, allow claiming the reward in batches.

# [02] Arithmetic rounding

The project is performing division before multiplication on multiple instances.

For example, looking at the calculation on [L1155](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155).

```
uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
```

If the `amount` is small and the `timelockMultiplier` is large, `points` can round down to zero. E.g.

```
amount = 1e15
timelockMultiplier = 1000
_DIVISOR = 100

points = 1e15 * 100 / 1e18 * 1000 / 100;
points = 0
```

By performing the division by last, `points` will not round down.

```
uint256 points = amount * 100 * timelockMultiplier / 1e18  / _DIVISOR;
points = 1e15 * 100 * 1000 / 1e18 / 100
points = 1
```

All the following instances are susceptible to arithmetic rounding and accounting errors.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1623

Note: It's unlikely to be an issue for [L1388](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388), since `_PRECISION` is 1e12 and `pool.totalPoints` ranges between 200 to 2000 on the existing tests. However if `pool.totalPoints` becomes a large value and `points` is small, this calculation can also round down.

## Recommendation

Perform the multiplications first, and the divisions last.

# [03] Check if `_to` is different than `_from` in `NeoTokyoStaker._assetTransferFrom()`

Some tokens might not check if `_to` is different than `_from` inside the `transferFrom()`. This could cause unexpected behavior when users can transfer funds to themselves. Consider adding the following check on `NeoTokyoStaker._assetTransferFrom()`

```
if (_to == from) {
  revert CustomError()
}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L766-L786

# [04] Emit events before external calls

Wherever possible, consider emitting events before external calls. In case of reentrancy, funds are not at risk (for external call + event ordering), however emitting events after external calls can damage frontends and monitoring tools in case of reentrancy attacks.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L101-L105

# [05] Pragma float

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2

# [06] Missing address zero checks on `NeoTokyoStaker` constructor

If one of the address variables gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L606

# [07] Enum check should be `> 3` instead of `> 4`

`AssetType` will be enumerated from 0 to 3. The upper bound check needs to replace 4 with 3. E.g. `uint8(_assetType) > 4` to `uint8(_assetType) > 3`.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668

# [08] Lack of checks-effects-interactions

Where possible, consider making state changes before external calls/transfer, to follow the checks-effects-interactions pattern.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L927-L929

# [09] Return named variables can be used instead of returning manual variables

It's possible to declare the variable on the function header and the it will be returned automatically.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L864

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1453

# [10] Remove the space on function parenthesis

`function getCreditYield (` to `function getCreditYield(`

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L625

# [11] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instance bellow shows private above external.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1597

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1659

# [12] Named imports

It's possible to name the imports to improve code readability. E.g. `import "@openzeppelin/contracts/token/ERC20/ERC20.sol";` can be rewritten as `import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";`

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L4-L8

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L4-L9
