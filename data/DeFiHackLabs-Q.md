Neo Tokyo GA Report

### [L1] Constructors and functions without contract integrity check

## Impact
It is recommended to add a "checkContract" function to verify the integrity of the contract code during the deployment process. This can help prevent attacks that aim to exploit vulnerabilities in the contract's code or its dependencies. By including an integrity check, the contract can ensure that it is running as intended and that it has not been tampered with or modified in any way.

## Proof of Concept
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-L85
Missing zero address check in constructor()
```
	constructor (
		address _bytes,
		address _s1Citizen,
		address _staker,
		address _treasury
	) {
		BYTES1 = _bytes;
		S1_CITIZEN = _s1Citizen;
		STAKER = _staker;
		TREASURY = _treasury;
	}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L165
Missing zero address check in changeStakingContractAddress()
```
changeStakingContractAddress()
	function changeStakingContractAddress (
		address _staker
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		STAKER = _staker;
	}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L176
Missing zero address check in changeTreasuryContractAddress()
```
	function changeTreasuryContractAddress (
		address _treasury
	) hasValidPermit(UNIVERSAL, ADMIN) external {
		TREASURY = _treasury;
	}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L605
```
	constructor (
		address _bytes,
		address _s1Citizen,
		address _s2Citizen,
		address _lpToken,
		address _identity,
		address _vault,
		uint256 _vaultCap,
		uint256 _noVaultCap
	) {
		BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
		LP = _lpToken;
		IDENTITY = _identity;
		VAULT = _vault;
		VAULT_CAP = _vaultCap;
		NO_VAULT_CAP = _noVaultCap;
	}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1708-L1714
```
	function configureLP (
		address _lp
	) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
		if (lpLocked) {
			revert LockedConfigurationOfLP();
		}
		LP = _lp;
```
## Tools Used
Manual

## Recommended Mitigation Steps
It is recommended to add a "checkContract" function.
Example:

```
function checkContract(address _account) internal view {
   require(_account != address(0), "Account cannot be zero address");

   uint256 size;
   // solhint-disable-next-line no-inline-assembly
   assembly { size := extcodesize(_account) }
   require(size > 0, "Account code size cannot be zero");
}

```

### [L2] Missing events for critical parameter changing operations by UNIVERSAL & ADMIN

## Impact
Change critical parameters should emit events and have timelocks. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

## Proof of Concept
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1708-L1714

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1721

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1760

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1783

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1802

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1819

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1851

## Tools Used
Manual

## Recommended Mitigation Steps
Consider emitting events 

### [L3] Gas griefing/theft is possible on unsafe external call

## Impact
Return data (bool success,) has to be stored due to EVM architecture, if in a usage like below, ‘out’ and ‘outsize’ values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided

## Proof of Concept
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L773

```
function _assetTransferFrom (
		address _asset,
		address _from,
		address _to,
		uint256 _idOrAmount
	) private {
		(bool success, bytes memory data) = 
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_FROM_SELECTOR,
					_from,
					_to, 
					_idOrAmount
				)
			);
```
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L802
```
function _assetTransfer (
		address _asset,
		address _to,
		uint256 _amount
	) private {
		(bool success, bytes memory data) = 
			_asset.call(
				abi.encodeWithSelector(
					_TRANSFER_SELECTOR,
					_to, 
					_amount
				)
			);
```
## Tools Used
Manual

## Recommended Mitigation Steps
Use a low-level assembly call since it does not automatically copy return data to memory. Consider to use https://github.com/nomad-xyz/ExcessivelySafeCall/blob/main/src/ExcessivelySafeCall.sol

---

### [N1] Emply functions - Maybe it's ok

	/**
		This function is called by the S1 Citizen contract when a new citizen is 
		minted. For historical reasons it must be left here as a stub and cannot be 
		entirely removed, though now it remains as a no-op.


https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L189
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L204
