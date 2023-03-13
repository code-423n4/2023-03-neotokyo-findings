### 1. Could the structs StakedS1Citizen and StakedS2Citizen be the same?

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L394-L398](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L394-L398)

Could you consider struct StakedS2Citizen equalizing StakedS1Citizen? If these structs are the same, the whole code can be simplified.

```jsx
struct StakedS2Citizen {
    uint256 stakedBytes;
    uint256 timelockEndTime;
    uint256 points;
    uint256 stakedVaultId;
    bool hasVault;    
}
```

### 2. Could you replace `_stringEquals` of Openzeppelin’s `Strings.sol`?

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L824-L865](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L824-L865)

Openzeppelin’s `Strings.sol` has the function `equal` that is simpler.

[https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Strings.sol#L82-L84](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Strings.sol#L82-L84)

### 3. Is it necessary to multiply 100 and divide 100?

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1022](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1022)

The value is multiplied by 100 and divided by 100 because _DIVISOR = 100. Does this make sense?

```jsx
citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
```

### 4. `1e18` should be constant or get from LP tokens decimal.

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155)

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1623](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1623)

Numeric variables should be constant.

### 5. Early return can reduce nesting

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1274](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1274)

The function is too nested and complicated, so you should consider returning early.

```jsx
if (pool.totalPoints == 0) return (0, 0);
```

### 6. Isn’t it necessary to check underflow?

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1320](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1320)

if i = 0, `i - 1` cause underflow. Isn’t it necessary to check underflow?

(In that code it is always 0 or higher, no problem.)

### 7. The value of mapping can be deleted by `delete` method.

[https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517-L1521](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517-L1521)

The value of mapping can be delete as shown below.

```jsx
delete stakedS1[msg.sender][citizenId];
```

Also stakedCitizen isn’t needed to be storage.

```jsx
StakedS1Citizen memory stakedCitizen = stakedS1[msg.sender][citizenId];
```

It will make the code a little bit simple but it doesn’t reduce gas cost.