## Balance double check

There is a check for sufficient user balance twice
The BYTES1 contract in the burn method has a `require` for this

```solidity
contracts/staking/BYTES2.sol

96:		if (IERC20(BYTES1).balanceOf(msg.sender) < _amount) {
97:			revert DoNotHaveEnoughOldBytes(_amount);
98:		}

101:		IByteContract(BYTES1).burn(msg.sender, _amount); 
```
---
## SWC-131 Unused variable

```solidity
contracts/staking/BYTES2.sol

46:	address immutable public S1_CITIZEN;
```
the variable is initialized but not used anywhere

---
## There is no direct connection between the implementation and the interface

```solidity
contracts/staking/BYTES2.sol

34:   contract BYTES2 is PermitControl, ERC20("BYTES", "BYTES")

contracts/staking/NeoTokyoStaker.sol
188: contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

```
Contract `BYTES2` must follow the interface `IByteContract`.
Contract `NeoTokyoStaker` must follow the interface `IStaker`.

To avoid possible non-compliance with the interface contract. And adding a compile-time check for compliance
Example:
```solidity
contract NeoTokyoStaker is IStaker, PermitControl, ReentrancyGuard
```
```solidity
contract BYTES2 is IBytesContract, PermitControl, ERC20("BYTES", "BYTES")
```
---

## Missed events for important events
Violation of standard practices for throwing events at important events. That makes it difficult to catch important actions

```solidity
contracts/staking/BYTES2.sol

162:	function changeStakingContractAddress
173:	function changeTreasuryContractAddress

contracts/staking/NeoTokyoStaker.sol

1708:	function configureLP
1721:	function lockLP
1737:	function configureTimelockOptions
1760:	function configureIdentityCreditYields
1783:	function configureIdentityCreditPoints 
1802:	function configureVaultCreditMultipliers
1819:	function configurePools
1851:	function configureCaps

```
Example:
User would like to track the moment of the configuration to be the first to stake and get an increased reward for the first blocks, etc.

User would like to know the moment when the administration will change something in order to quickly react to it

## Someone can call `getReward` for another user
A method call by user A for user B may cause user B can to miss out on some benefit (using the windows issue for earning extra rewards as an example)

```solidity
contracts/staking/BYTES2.sol

114:	function getReward (
115:		address _to
116:	) external {
```

---
## A difficult to understand way of comparing two strings

```solidity
contracts/staking/NeoTokyoStaker.sol

824:	function _stringEquals (
		string memory _a,
		string memory _b
	) private pure returns (bool) {
		bytes memory a = bytes(_a);
		bytes memory b = bytes(_b);
		
		// Check equivalence of the two strings by comparing their contents.
		bool equal = true;
		assembly {
			let length := mload(a)
			switch eq(length, mload(b))

			// Proceed to compare string contents if lengths are equal. 
			case 1 {
				let cb := 1

				// Iterate through the strings and compare contents.
				let mc := add(a, 0x20)
				let end := add(mc, length)
				for {
					let cc := add(b, 0x20)
				} eq(add(lt(mc, end), cb), 2) {
					mc := add(mc, 0x20)
					cc := add(cc, 0x20)
				} {

					// If any of these checks fails then arrays are not equal.
					if iszero(eq(mload(mc), mload(cc))) {
						equal := 0
						cb := 0
					}
				}
			}

			// By default the array length is not equal so the strings are not equal.
			default {
				equal := 0
			}
		}
		return equal;
	}

```

Can be replaced by:

```solidity
    function equal(string memory a, string memory b) internal pure returns (bool) {
        return keccak256(bytes(a)) == keccak256(bytes(b));
    }

```
---

## Condition is always `FALSE`

The compiler automatically adds checks for the correctness of data transmission. on we transfer a number that is reduced to ENUM, and will not allow the transfer of a number greater than the limit. Therefore, this condition will never be fulfilled due to the check that will be added to the bytecode by the compiler

```solidity
contracts/staking/NeoTokyoStaker.sol

1205:		if (uint8(_assetType) > 4) {
1206:			revert InvalidAssetType(uint256(_assetType));
1207:		}

1668:		if (.... || uint8(_assetType) > 4) {
1669:			revert InvalidAssetType(uint256(_assetType));
1670:		}
```
