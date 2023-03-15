
- [Gas](#gas)
    - [**1. Optimize stringEquals**](#1-optimize-stringequals)
    - [**2. Optimize stake**](#2-optimize-stake)

# Gas

## **1. Optimize `stringEquals`**

It is possible to optimize the comparison of 2 string using a hash function.

**Sample:**

```js
pragma solidity ^0.8.19;

contract testA
{
	function stringEquals (
		string memory _a,
		string memory _b
	) public pure returns (bool) {
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
}

contract testB
{
	function stringEquals (
		string memory _a,
		string memory _b
	) public pure returns (bool) {
        bytes memory a = bytes(_a);
		bytes memory b = bytes(_b);
		if (a.length != b.length) return false;
		return keccak256(a) == keccak256(b);
	}
}
```

With my result it's cheaper using `keccak256`:

```diff
+ With one character ("a") or 32 characters ("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"): 1251 before, 1173 after
+ With 64 characters ("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"): 1356 before, 1197 after
```

**Because is only used for `if (_stringEquals(class, "Hand of Citadel")) {` that is less than 32 bytes, it will be cheaper using `keccak256`**

**Affected source code:**

- [NeoTokyoStaker.sol:824](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L824)

## **2. Optimize `stake`**

The following logic is redundant an can be removed because it's not possible to send a different type and also it's checked that the `rewardCount` has a value with `_pools[_assetType].rewardCount == 0`.

```diff
	function stake (
		AssetType _assetType,
		uint256 _timelockId,
		uint256,
		uint256,
		uint256
	) external nonReentrant {

-		// Validate that the asset being staked is of a valid type.
-		if (uint8(_assetType) > 4) {
-			revert InvalidAssetType(uint256(_assetType));
-		}

		// Validate that the asset being staked matches a configured pool.
		if (_pools[_assetType].rewardCount == 0) {
			revert UnconfiguredPool(uint256(_assetType));
		}
```

**Affected source code:**

- [NeoTokyoStaker.sol:1205-1207](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205-L1207)