# Low Severity Issues

## `getReward` lacks access control

`getRewards` must be executed by the citizen contract to update and claim rewards. But it lacks access control, and can be executed by anyone.

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L114

```
   /**
	This function is called by the S1 Citizen contract to emit BYTES to callers 
	based on their state from the staker contract.

	@param _to The reward address to mint BYTES to.
    */
    function getReward (	
	address _to
    ) external
```

## `hasRight` will revert at expiration timestamp

In PermitControl, a user is given permission for a role until an expiration time. But in function `hasRight`, the condition `permissions[_address][_circumstance][_right] > block.timestamp` does not include the expiration time. So the function will revert at expiration time.

```
	function hasRight (
		address _address,
		bytes32 _circumstance,
		bytes32 _right
	) public view returns (bool) {
		return permissions[_address][_circumstance][_right] > block.timestamp;
	}
```

Change the condition to include the expiration time

```
    permissions[_address][_circumstance][_right] >= block.timestamp
```

## `_expirationTime` is not validated

In setPermit function, expiration time is not validated. So it can be set in the past

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/access/PermitControl.sol#L144

```
	function setPermit (
		address _address,
		bytes32 _circumstance,
		bytes32 _right,
		uint256 _expirationTime
	) public virtual hasValidPermit(UNIVERSAL, managerRight[_right]) {
		require(_right != ZERO_RIGHT, "P2");
		permissions[_address][_circumstance][_right] = _expirationTime;
		emit PermitUpdated(
			_msgSender(),
			_address,
			_circumstance,
			_right,
			_expirationTime
		);
	}
```

## Owner can renounce ownership

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/s1/NTCitizenDeploy.sol#L328

```
    function renounceOwnership() public virtual onlyOwner {
        _setOwner(address(0));
    }
```

## Ownership transfer can be a 2-step process

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/s1/NTCitizenDeploy.sol#L336

```
    function transferOwnership(address newOwner) public virtual onlyOwner {
        require(newOwner != address(0), "Ownable: new owner is the zero address");
        _setOwner(newOwner);
    }
```

## Precision loss in points calculation

Performing division before multiplication can cause intermediate results to truncate and will lead to smaller value

Consider performing multiplication before division

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1155

```
			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;	
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1388

```
			uint256 share = points * _PRECISION / pool.totalPoints * totalReward;

```

## Array lengths are not validated

In configuration functions array lengths are not validated. If array lengths are not validated, it may cause unexpected behaviour.

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1737

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1760

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1783

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1802

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1819

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1851

# Non Critical Issues

## Import declarations should import specific identifiers, rather than the whole file

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L4

```
    import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

    import "../access/PermitControl.sol";
    import "../interfaces/IByteContract.sol";
    import "../interfaces/IStaker.sol";
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/access/PermitControl.sol#L4

```solidity
    import "@openzeppelin/contracts/access/Ownable.sol";
    import "@openzeppelin/contracts/utils/Address.sol";
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L4

```solidity
    import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

    import "../access/PermitControl.sol";
    import "../interfaces/IByteContract.sol";
    import "../interfaces/IGenericGetter.sol";
```

## Unnecessary validation for type AssetType

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205

```solidity

	enum AssetType {
		S1_CITIZEN,
		S2_CITIZEN,
		BYTES,
		LP
	}

	function stake (
		AssetType _assetType,
		uint256 _timelockId,
		uint256,
		uint256,
		uint256
	) external nonReentrant {

		// Validate that the asset being staked is of a valid type.
		if (uint8(_assetType) > 4) {	
			revert InvalidAssetType(uint256(_assetType));
		}
```

## Use delete keyword 

Use delete keyword instead of setting the variable to default values

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1517

```
		stakedCitizen.stakedBytes = 0;
		stakedCitizen.timelockEndTime = 0;
		stakedCitizen.points = 0;	
		stakedCitizen.hasVault = false;
		stakedCitizen.stakedVaultId = 0;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1582

```
		stakedCitizen.stakedBytes = 0;
		stakedCitizen.timelockEndTime = 0;
		stakedCitizen.points = 0;
```

