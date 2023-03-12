[QA-01\] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USED TO IMMUTABLE RATHER THAN CONSTANT
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the construct.

```
BYTES2.sol:
37: bytes32 public constant BURN = keccak256("BURN");

40: bytes32 public constant ADMIN = keccak256("ADMIN");

```

```
NeoTokyoStaker.sol:
206: bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");;

209:	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
        "CONFIGURE_TIMELOCKS"
    );
    
214:	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

216:	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
    
220:	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```

\[QA-02\] Use stable pragma statement
Using a floating pragma ^0.8.19 statement is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode.
The contract should not use floating pragma, e.g. (\*0.6.0 or >=0.4.0 \*0.6.0), which allows a range of compiler versions. It is important to lock the pragma (for example, not using ^ in pragma solidity 0.8.19) to prevent contracts from being accidentally deployed using an older compiler with unfixed bugs.

```
File: NeoTokyoStaker.sol
2:  pragma solidity ^0.8.19;
```

```
File: BYTES2.sol
2:  pragma solidity ^0.8.19;
```

\[QA-03\] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

```
File: BYTES2.sol
4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "../access/PermitControl.sol";
8: import "../interfaces/IByteContract.sol";
9: import "../interfaces/IStaker.sol";```

```

```
File:NeoTokyoStaker.sol
4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

6: import "../access/PermitControl.sol";
7: import "../interfaces/IByteContract.sol";
8: import "../interfaces/IGenericGetter.sol";
```