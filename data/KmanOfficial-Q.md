[LOW-1] Follow Checks-Effects-Interactions Pattern to reduce control/attack vector

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol #L875, #L995 #L1044, #L1124, #L1196, #L1459, #L1534, 1597 #1659

In the various functions dealing with the transfer of assets (LP, NFTs, Rewards) within `NeoTokoyoStaker` through interlinked function calls such as

`NeoTokyoStaker._stakeS1Citizen/NeoTokyoStaker._stakeS2Citizen/NeoTokyoStaker._stakeBytes/NeoTokyoStaker._stakeLP` via `NeoTokyoStaker.stake` and `NeoTokyoStaker._withdrawS1Citizen/NeoTokyoStaker._withdrawS2Citizen/NeoTokyoStaker._withdrawLP` via `NeoTokyoStaker.withdraw`

they utilise `NeoTokyoStaker._assetTransferFrom` and `NeoTokyoStaker._assetTransfer`. It may be good practice to deal with the effects of each functionality such as staking the asset, withdrawing the asset etc first and then call these assetTransfer functions in order to ensure there is no possible event for reentrancy and so that storage within the contract is controlled before interacting with other contracts. It may not be very likely for most assets and deemed safe as most interactions are with contracts of the ecosystem, but the address held at the LP for example could be updated to a new contract and in future this is an example of potentially where there could be issues. After updating storage, when the various assetTransfer functions are called, it will revert if msg.sender does not own the asset or doesn't have enough of that asset so there is no ability for the system to be tricked by following this design pattern.

-----

[N-01] Update comment on `BYTES2.getReward`
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L108-L129

The following comment for this function is `/**
		This function is called by the S1 Citizen contract to emit BYTES to callers 
		based on their state from the staker contract.

		@param _to The reward address to mint BYTES to.
	*/`

The `getReward` function is called by S1 Citizen contract, but also via the `NeoTokyoStaker.stake` and `NeoTokyoStaker.withdraw` functions too granting user's their total rewards with each option. I believe this comment was added based on previous BYTES1 contract's getReward function logic was coded and for understanding the protocol going forward it should be modified.

-----



