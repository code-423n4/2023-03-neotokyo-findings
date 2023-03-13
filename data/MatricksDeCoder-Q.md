L-1 Missing events for critical change of contract functionality and or parameters especially those involving priviledged roles. It is important for contracts to emit events for off chain monitoring, transparency, auditability, reporting etc into key changes of their functioning.
[Bytes.sol line 162-166 changeStakingContractAddress()] changes staking address does not emit event
[Bytes.sol line 173-177 changeTreasuryContractAddress()] changes treasury address does not emit event
[NeoTokyoStaker.sol line 1708-1715 configureLP()] configures new LP Token but does not emit event
[NeoTokyoStaker.sol line 1721- 1723 lockLP()] locks LP Token but does not emit event
[NeoTokyoStaker.sol line 1851- 1859 configureCaps()] configures Caps on vaulted & unvaulted Citizens but does not emit event


L-2 Time Delays on Critical Changes of Parameters is missing. There are certain functions that change the functioning of contracts e.g the staking contracts for BYTES2.sol, the Treasury etc and for NeoTokyoStaker.sol the caps on staking vaults, Liquidity Pool Token etc. These changes by priviledged roles are immediate which may not be ideal as it does not give warning to users or chances for rereview of new values. It may be ideal to have time delay on such changes using Timelock functionalities. 
[Bytes.sol line 162-166 changeStakingContractAddress()] 
[Bytes.sol line 173-177 changeTreasuryContractAddress()] 
[NeoTokyoStaker.sol line 1708-1715 configureLP()] 
[NeoTokyoStaker.sol line 1721- 1723 lockLP()] 
[NeoTokyoStaker.sol line 1851- 1859 configureCaps()]


L-3 Missing zero address validation. Certain functions do not validate inputs for addresses to check if address(0) is not passed. Passing of zero addresses may lead to protocol not working as expected e.g burning of tokens, success return values for low level calls when contracts dont exist at address(0). It is recommended to check relevant address inputs are not zero addresses e.g require(addressInput != address(0), "error string") or if(addressInput == address(0) revert ZERO_ADDRESS) etc 
[Bytes.sol line 115 ] address _to not checked for address(0)
[Bytes.sol line 163 ] address _staker not checked for address(0)
[Bytes.sol line 174 ] address _treasury not checked for address(0)
[NeoTokyoStaker.sol line 797,798] address _asset, address _to not checked for address(0)
[NeoTokyoStaker.sol line 1709] address _lp not checked for address(0)


L-4 Missing equality checks in setter functions. It is considered best practice to make sure setter functions check that old values are different from new values. This allows contracts not to expect setting functions made changes to protocol when nothing changed. Additionally is also prevents gas losses from calling functions for not impact on the contracts. It is recommended to compare old value and new Value if they are the same, revert the function. 
eg in the relevant functions below check e.g if(CURRENT_STATE_VALUE == _NEW_VALUE) { revert NO_CHANGE()}
[Bytes.sol line 163] check that STAKER != _staker
[Bytes.sol line 174] check that TREASURY != _treasury
[NeoTokyoStaker.sol line 1709] check that LP != _lp
[NeoTokyoStaker.sol line 1709] check that LP != _lp
[NeoTokyoStaker.sol line 1851- 1859 configureCaps()] check that VAULT_CAP != _vaultedCap, NO_VAULT_CAP = _unvaultedCap
 

NC-1 Unlocked Pragma - It is recommended best practice to make use of locked pragma e.g solidity 0.8.19 to ensure contracts are tested, compiled and deployed using the same compiler version
Bytes2.sol line 2 pragma solidity ^0.8.19;
NeoTokyoStaker.sol line 2 pragma solidity ^0.8.19;


NC-2 Spelling, typos and grammar - These may impact the interpretation of the code by auditors, users, dev s etc impacting the readability, maintanability and usability of the code. It is important to ensure comments have correct grammar, spelling and give intended meaning
[NeoTokyoStaker.sol line 11] => "Thrown during when attempting to operate on a non-existent Citizen (S1 or S2)" => "Thrown when attempting to operate on a non-existent Citizen (S1 or S2)
[NeoTokyoStaker.sol line 417] =>  tiume  should be time
[NeoTokyoStaker.sol line 535] => ctiizen should be citizen
[NeoTokyoStaker.sol line 654] => configued  should be configured
[NeoTokyoStaker.sol line 1373] => entireity should be entirety
[NeoTokyoStaker.sol line 1776] => funciton should be function 


NC-3 Function naming that appears like getter functions but is a function with side effects that does something else. Most readers of contracts may associate functions starting with get... as getter functions that are view functions to read from the state of contracts. It is recommended to name functions appropriately 
[Bytes.sol line 114-123 getReward()] -may be ideal to rename to something like redeemReward








