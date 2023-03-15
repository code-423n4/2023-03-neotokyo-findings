#Low Risk and Non-Critical Issues

##NeoTokyoStaker::constructor: missing zero address check for in constructor for immutable variables
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588-L606

Constructors should check the address written in an immutable address variable is not the zero address.
This include `BYTES`, `S1_CITIZEN`, `S2_CITIZEN`, `IDENTITY` and `VAULT`

```solidity

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
	//@audit-ok - no zero address check
	BYTES = _bytes;			//IMMUTABLE
	S1_CITIZEN = _s1Citizen;	//IMMUTABLE
	S2_CITIZEN = _s2Citizen;	//IMMUTABLE
	LP = _lpToken;
	IDENTITY = _identity;	 	//IMMUTABLE
	VAULT = _vault;			//IMMUTABLE	
	VAULT_CAP = _vaultCap;
	NO_VAULT_CAP = _noVaultCap;
	}
```

#Gas Optimizations
##NeoTokyoStaker::getStakerPositions: no zero address check cause unnecessary code execution
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L710
By adding a zero address check at the beginning of the function, this will prevent from executing the two for loops and memory allocation

##NeoTokyoStaker::_withdrawS2Citizen & _withdrawS1Citizen: Reorder the revert conditions for gas saving
The first require statement will be true even if the second one is false. There's no need to check if timelock is cleared if nothing is staked

```solidity
	function _withdrawS2Citizen () private {
		uint256 citizenId;
		assembly {
			citizenId := calldataload(0x24)
		}

		// Validate that the caller has cleared their asset timelock.
		StakedS2Citizen storage stakedCitizen = stakedS2[msg.sender][citizenId];
		if (block.timestamp < stakedCitizen.timelockEndTime) {
			revert TimelockNotCleared(stakedCitizen.timelockEndTime);
		}

		//@audit - I would have put this condition first, as there's no need to check if timelock is cleared if nothing is staked
		// Validate that the caller actually staked this asset.
		if (stakedCitizen.timelockEndTime == 0) {
			revert CannotWithdrawUnownedS2(citizenId);
		}
```

```
@@ -1543,6 +1543,9 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
 
                // Validate that the caller has cleared their asset timelock.
                StakedS2Citizen storage stakedCitizen = stakedS2[msg.sender][citizenId];
+               if (block.timestamp < stakedCitizen.timelockEndTime) {
+                       revert TimelockNotCleared(stakedCitizen.timelockEndTime);
+               }
 
                //@audit - I would have put this condition first, as there's no need to if timelock is cleared if nothing is staked
                // Validate that the caller actually staked this asset.
@@ -1550,11 +1553,6 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        revert CannotWithdrawUnownedS2(citizenId);
                }
 
-               if (block.timestamp < stakedCitizen.timelockEndTime) {
-                       revert TimelockNotCleared(stakedCitizen.timelockEndTime);
-               }
-
-
                // Return any staked BYTES.
                if (stakedCitizen.stakedBytes > 0) {
                        _assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);

```