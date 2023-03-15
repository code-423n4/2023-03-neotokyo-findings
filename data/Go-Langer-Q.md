### No check in place referencing the function call exclusive to the Citizen Contract

Bytes2.sol Function getReward

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L114

	
The following comment is mentioned above the function: 
"This function is called by the S1 Citizen contract to emit BYTES to callers based on their state from the staker contract."

	

	function getReward (address _to) external {
		(
			uint256 reward,
			uint256 daoCommision // @audit - @todo Review where this is used.
		) 
		= IStaker(STAKER).claimReward(_to);

		// Mint both reward BYTES and the DAO tax to targeted recipients.
		if (reward > 0) {
			_mint(_to, reward);
		}
		if (daoCommision > 0) {
			_mint(TREASURY, daoCommision);
		}
	}



If the intention is to only calling of this function by the s1Citizen contract, as per the comments above, then you should insert a require statement. Currently, any user or EOA can call this contract. Use below example if it is indeed intentional for the Citizen Contract to call this function.
		
```require(msg.sender == S1_CITIZEN, "Only the S1 Citizen can call this.");```



### Function reverts if assetType is not Valid

Function stake NeoTokyoStaker.sol

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1196

If the ID of the _asset is ever equal to 4, this function will not revert. If its not the intention to do so, then perhaps refactor the below code.

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

		refactored to: 
		
		if (uint8(_assetType) >= 4) {
			revert InvalidAssetType(uint256(_assetType));
		}


With this change, the function will now throw an exception with the InvalidAssetType message if _assetType is equal to or greater than 4, which ensures that only valid asset types are accepted in the contract.

### Function getReward in NTCitizenDeploy calls updateReward, which can only be called by citizenContract

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/s1/NTCitizenDeploy.sol#L1621

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/s1/BYTESContract.sol#L939

```
 function getReward() external {
        IByteContract byteToken = IByteContract(bytesContract);
        byteToken.updateReward(msg.sender, address(0), 0);
		byteToken.getReward(msg.sender);
	}
```

```
 function updateReward(address _from, address _to, uint256 _tokenId) external {
		require(msg.sender == address(citizenContract));
```

Consider adding in the same functionality here in the getReward Function, that is also comprised in the updateReward function:

			"require(msg.sender == address(citizenContract));"

### Incorrect loop definition

function getStakerPositions NeoTokyoStaker

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L710

```for (uint256 i; i < _stakerS1Position[_staker].length; )```

replace with:

```for (uint256 i = 0; i < _stakerS1Position[_staker].length; i++).```

### Uninitialized local variables

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1264

function getPoolReward

The below variable is not being initialized properly.

```uint256 totalReward;```

