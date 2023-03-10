# [N-01] Incorrect description in NatSpec comment of parameter
## Context 
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L70

```
		@param _bytes The address of the BYTES 2.0 ERC-20 token contract.
		@param _s1Citizen The address of the assembled Neo Tokyo S1 Citizen.
		@param _staker The address of the new BYTES emitting staker.
		@param _treasury The address of the DAO treasury.
	*/
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
## Description
The parameter specifies the ```_bytes``` parameter is of type ```BYTES 2.0 ERC-20``` however, according to the code it is intended to be of type BYTES 1.0 as shown in the line ```BYTES1 = _bytes;``` and the address is used to in the ```upgradeBytes``` function to burn the old BYTES 1.0 tokens.

## Resolution
Correct the description in the NatSpec comment

# [N-02] Input parameters are not checked in constructor
## Context 
Constructor function:BYTES2.sol
```
		@param _bytes The address of the BYTES 2.0 ERC-20 token contract.
		@param _s1Citizen The address of the assembled Neo Tokyo S1 Citizen.
		@param _staker The address of the new BYTES emitting staker.
		@param _treasury The address of the DAO treasury.
	*/
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
## Description
The input values into the constructor are never checked in any way and the rest of the code in the contract assumes the values to be correct.

## Resolution
Check the input addresses for address(0) and perhaps that they are indeed contracts.
