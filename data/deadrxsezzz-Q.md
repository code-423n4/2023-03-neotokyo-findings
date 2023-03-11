# Use of Floating pragma

The contracts should be deployed with the same solidity version with which they have been tested. Locking the pragma ensures that the contracts don't get deployed with an older version which might introduce bugs.

Recommended Mitigation Steps
Use fixed pragma


# Use all multiplication before division

Dividing before all multiplication has been done can lead to lack of precision. Therefore, the following should be changed

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1388

into 				`uint256 share = points * _PRECISION * totalReward / pool.totalPoints;`

# For modern and more readable code; update import usages
Context:
 
` import "../access/PermitControl.sol";
import "../interfaces/IByteContract.sol";
import "../interfaces/IGenericGetter.sol";
import "../access/PermitControl.sol";
import "../interfaces/IByteContract.sol";
import "../interfaces/IStaker.sol";`

Description:
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.
This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.

Recommendation:
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;

`` import {Owned} from "solmate/auth/Owned.sol";  
import {ERC721} from "solmate/tokens/ERC721.sol";  
import {LibString} from "solmate/utils/LibString.sol";  
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";  
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";  
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";  
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";  ``