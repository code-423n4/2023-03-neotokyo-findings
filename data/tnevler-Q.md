# Report
## Low Issues ##
### [L-1]: Revert if both conditions are not met
**Context:**

first condition: ```if (reward > 0) {``` [L123](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L123)

second condition: ```if (daoCommision > 0) {``` [L126](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L126)

**Recommendations: **

Revert function if (reward == 0 & daoCommision == 0).

## Non-Critical Issues ##
### [N-1]: Use of immutable instead of constant keccak expression
**Context:**

1. ```bytes32 public constant BURN = keccak256("BURN");``` [L37](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37) 
1. ```bytes32 public constant ADMIN = keccak256("ADMIN");``` [L40](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L40) 
1. ```bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");``` [L206](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206) 
1. ```bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(``` [L209](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L209) 
1. ```bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");``` [L214](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L214) 
1. ```bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");``` [L217](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L217) 
1. ```bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");``` [L220](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L220) 

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/contracts.html#constant-and-immutable-state-variables) for a constant variable, the expression assigned to it is copied to all the places where it is accessed and also re-evaluated each time. It is recommended to use immutable instead. 

### [N-2]: Follow Solidity standard naming conventions
**Context:**

1. ```address immutable public BYTES1;``` [L43](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L43) (variable name must be in mixedCase)
1. ```address immutable public S1_CITIZEN;``` [L46](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L46) (variable name must be in mixedCase)
1. ```address public STAKER;``` [L49](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L49) (variable name must be in mixedCase)
1. ```address public TREASURY;``` [L52](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L52) (variable name must be in mixedCase)
1. ```address immutable public BYTES;``` [L223](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L223) (Variable name must be in MixedCase)
1. ```address immutable public S1_CITIZEN;``` [L226](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L226) (Variable name must be in MixedCase)
1. ```address immutable public S2_CITIZEN;``` [L229](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L229) (Variable name must be in MixedCase)
1. ```address public LP;``` [L232](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L232) (Variable name must be in MixedCase)
1. ```address immutable public IDENTITY;``` [L239](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L239) (Variable name must be in MixedCase)
1. ```address immutable public VAULT;``` [L242](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L242) (Variable name must be in MixedCase)
1. ```uint256 public VAULT_CAP;``` [L249](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L249) (Variable name must be in MixedCase)
1. ```uint256 public NO_VAULT_CAP;``` [L255](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L255) (Variable name must be in MixedCase)

**Description:**

The above codes don't follow [Solidity's standard naming convention](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#naming-conventions).  
- Internal and private functions should use mixedCase format starting with an underscore.
- Structs should be named using the CapWords style. Examples: MyCoin, Position, PositionXY.
- Events should be named using the CapWords style. Examples: Deposit, Transfer, Approval, BeforeTransfer, AfterTransfer.
- Functions should use mixedCase. Examples: getBalance, transfer, verifyOwner, addMember, changeOwner.
- Function arguments should use mixedCase. Examples: initialSupply, account, recipientAddress, senderAddress, newOwner.
- Local and State Variable should use mixedCase. Examples: totalSupply, remainingSupply, balancesOf, creatorAddress, isPreSale, tokenExchangeRate.
- Constants should be named with all capital letters with underscores separating words. Examples: MAX_BLOCKS, TOKEN_NAME, TOKEN_TICKER, CONTRACT_VERSION.
- Modifier should use mixedCase. Examples: onlyBy, onlyAfter, onlyDuringThePreSale.
- Enums, in the style of simple type declarations, should be named using the CapWords style. Examples: TokenGroup, Frame, HashStyle, CharacterLocation.
