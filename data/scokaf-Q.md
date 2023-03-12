# 1: FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGE					

Vulnerability details

## Context:

Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.
This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces to allow us to apply this rule better.

## Proof of Concept

> ***File: BYTES2.sol***

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L4-L5

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L7-L9 

> ***File: NeoTokyoStaker.sol***

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L4


https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L6-L8

## Tools Used

Manual Analysis

## Recommended Mitigation Steps

import {contract1 , contract2} from "filename.sol";

#### Example:

import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";

# 2: PRAGMA VERSION ^0.8.19 VERSION TOO RECENT TO BE TRUSTED.

Vulnerability details

## Context:

Unexpected bugs can be reported in recent versions;
Risks related to recent releases
Risks of complex code generation changes
Risks of new language features
Risks of known bugs

0.8.19 (2023-02-22)
0.8.18 (2023-02-01)
0.8.17 (2022-09-08)
0.8.16 (2022-08-08)

For reference, see https://github.com/ethereum/solidity/blob/develop/Changelog.md  


## Proof of Concept

 > ***File: BYTES2.sol***

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L2

 > ***File: NeoTokyoStaker.sol***

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L2

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Use a non-legacy and more battle-tested version

Use 0.8.10
