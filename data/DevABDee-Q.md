# Qualitative Analysis:

| Metric | Rating |
| :-----: | :-----: |
| Test Coverage | Fine |
| Best Practices | Fine |
| Documentation | Bad |
| NatSpec Comments | Good |
| Code Quality | Good |
| Complexity | Bad |
| Decentralization | Excellent |

Overall, the codebase appears satisfactory, albeit with some areas for improvement. It is advisable for a small codebase to have comprehensive test coverage, which is not currently the case here. While best practices are being adhered to, there may be some gaps in this regard. Documentation appears to be lacking, although the NatSpec comments provided are commendably thorough. Code quality seems good, notwithstanding the complexity of the codebase, which may in part be attributed to the size of the main contract. It is worth considering dividing this contract into several smaller ones to enhance code clarity. Overall, the project's decentralized nature is consistent with expectations.

#### Quality Rating Scale:
Dead ----------- Poor -------------- Bad -------------- Fine ------------- Good ------------ Great ----------- Excellent
|-------------------|-------------------|-------------------|-------------------|-------------------|-------------------|

## Findings Summary:
| No | Issue |
| :-----: | :-----: |
| L-01 | The token holder will lose a token without providing any value to the treasury |
| L-02 | Checks-Effects-Interactions Pattern not implemented |
| L-03 | Avoid using low-level calls |
| L-04 | `_timelock` can be set as a timestamp that was in the past |
| L-05 | Loss of precision due to rounding |
| NC-1 | Floating Pragma & Multiple compilers versions defined |
| NC-2 | Very recent compiler version used - `0.8.19` |
| NC-3 | Imports not specific |
| NC-4 | Contract code size is 63151 bytes. Exceeds 24576 bytes size limit |
| NC-5 | Constructor events missing |
| NC-6 | Solidity function ordering guidelines not followed |
| NC-7 | Function argument/Input name not specified |
| I-01 | Insufficient test coverage |
| I-02 | Avoid non-serious comments in the codebase|
| I-03 | Use a single file system for constants |
| I-04 | Use a single file system for error |

# Low Findings:

## L-01: The token holder will lose a token without providing any value to the treasury.
In [BYTES2.burn()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L140) allows users to mint their tokens and provide value `treasuryShare` to the treasury. `treasuryShare` is calculated as:
```solidity
treasuryShare = _amount * 2 / 3;
```
If the `_amount` will be `1`, treasuryShare will be `0.66666`. So `0` traeasuryShare will be minted.
We can mint ERC20 tokens with a value of 0 to any Ethereum address. However, minting tokens with a value of 0 is not recommended, as it does not provide any real value to the token holder and may cause confusion or errors in token transfers and calculations.

The token holder will lose his 1 token without providing any value to the treasury. 
#### Migitation
Make sure that `_amount` is always greater than 1.
```solidity
require(_amount > 1, "Least 2 Tokens);
```

## L-02: Checks-Effects-Interactions Pattern not implemented.
[NeoTokyoStaker._withdrawS1Citizen](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1459) & [NeoTokyoStaker._withdrawS2Citizen](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1534) functions, do not implement CEI (Checks-Effects-Interactions) Pattern.

CEI pattern is a must-follow pattern, reentrancy guards cannot protect against cross-reentrancy (cross-function & cross-contracts) attacks.
#### Migitation
Implement CEI Pattern.

## L-03: Avoid using low-level calls.
Functions [NeoTokyoStaker._assetTransfer](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L796) & [NeoTokyoStaker._assetTransferFrom](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L766), use low-level calls to transfer ERC20 & ERC721 tokens. These low-level calls can be and should be avoided using ERC20.transfer/ERC20.transferFrom or ERC721.transfer/ERC721.transferFrom functions. 

## L-04: `_timelock` can be set as a timestamp that was in the past
These `NeoTokyoStaker.sol` functions, [_stakeLP](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1124), [_stakeS1Citizen](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L875) & [_stakeS2Citizen](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L995) doesn't make sure that `_timelock` is in the present/future. Users can input a timestamp that was in the past.

#### Mitigation:
Consider adding this check on all instances:
```solidity
require(_timelock >= block.timestamp, "PAST TIME")
```

## L-05: Loss of precision due to rounding
```solidity
contracts\staking\NeoTokyoStaker.sol:
   984  	citizenStatus.points = identityPoints * vaultMultiplier * timelockMultiplier / _DIVISOR / _DIVISOR;

  1040  	citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;

  1097  	uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

  1119  	uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

  1178  	uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

  1413  	uint256 share = points * _PRECISION / pool.totalPoints * totalReward;

  1651  	uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
```

#### Migitation
Add scalars


# Non-Critical Findings:
## NC-1: Floating Pragma & Multiple compilers versions defined
Contracts should be deployed with the same compiler version and flag that they have been tested thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

Lock the pragma.

In hardhat.config.js, there are two solidity compilers are defined.

Consider using only one compiler version.

See [SWC-103](https://swcregistry.io/docs/SWC-103) for more info.

## NC-2: Very recent compiler version used - `0.8.19`
`0.8.19` is a very recent solidity compiler and even it is not fully supported in hardhat yet. Using old versions of Solidity prevents the benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. Consider using one of these versions: 0.8.4, 0.8.7 or 0.8.8. 

## NC-3: Imports not specific 
Specific Named Imports improve readability.
```solidity
contracts\staking\BYTES2.sol:
   9: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
  10: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
  12: import "../access/PermitControl.sol";
  13: import "../interfaces/IByteContract.sol";
  14: import "../interfaces/IStaker.sol";

contracts\staking\NeoTokyoStaker.sol:
   9: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
  11: import "../access/PermitControl.sol";
  12: import "../interfaces/IByteContract.sol";
  13: import "../interfaces/IGenericGetter.sol";
```
Consider making these imports named specific:
```solidity
import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";
```

## NC-4: Contract code size is 63151 bytes. Exceeds 24576 bytes size limit
The `NeoTokyoStaker.sol` is almost 3 times bigger than the max contract size limit. Contracts get compiled using the optimizer but it just makes the contracts pretty complex & has difficult readability which makes the contract difficult to audit as well.

Consider distributing contract functions on multiple contracts to increase readability & audibility.

## NC-5: Constructor events missing 
To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event in the constructor

Instances: **All Contracts**.

## NC-6: Solidity function ordering guidelines not followed
PoC:
[_stakeS1Citizen](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L995), a `private` function, defined in the middle of the contract.
[configureCaps](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1851), an `external` function defined at the end of the contract. 

Consider following the [Solidity Function ordering Style Guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) to increase the readability of the contracts.
```
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- view and pure functions
```

## NC-7: Function argument/Input name not specified.
NeoTokyoStaker._stakeBytes & NeoTokyoStaker.withdraw, have function arguments (uint256) names not specified. Consider specifying it.


# Informational Findings:
### I-01: Insufficient test coverage
The test coverage rate of the project is ~80%. Testing all functions is best practice in terms of security criteria. For this small code, 100% or at least +95% Test coverage is expected. 

### I-02: Avoid non-serious comments in the codebase
There is a [non-serious comment](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L148) in the BYTES2.sol contract. 

### I-03: Use a single file system for constants
Consider using a single file system for constants to further improve code readability

### I-04: Use a single file system for error
Consider using a single file system for custom errors to further improve code readability 