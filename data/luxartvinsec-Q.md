# 1. Potential Reentrancy Attack Vulnerability in `BYTES2` and `NeoTokyoStaker` contract 

### Links : https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114
### https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1409
### Summary: 
The `BYTES2` contract contains a potential reentrancy attack vulnerability that could allow an attacker to repeatedly call the `getReward` function and drain the contract's funds.
### Impact: 
An attacker could repeatedly call the `getReward` function, causing the contract to repeatedly mint new BYTES tokens and drain the contract's funds. This could result in financial loss for the contract and its users.
### Recommendation: 
It is recommended to implement the checks-effects-interactions pattern to prevent reentrancy attacks. One way to do this is to use the OpenZeppelin `ReentrancyGuard` contract, which prevents reentrancy attacks by using a mutex to prevent a function from being called again until its previous invocation has completed. Additionally, the `getReward` function could be modified to only allow a user to call it once per block, or only allow a certain amount of tokens to be minted per call.
Example: 
The following modified `getReward` function implements the checks-effects-interactions pattern and uses the `ReentrancyGuard` contract from OpenZeppelin to prevent reentrancy attacks:
```
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract BYTES2 is PermitControl, ERC20("BYTES", "BYTES"), ReentrancyGuard {

  ...

  /**
		This function is called by the S1 Citizen contract to emit BYTES to callers 
		based on their state from the staker contract.

		@param _to The reward address to mint BYTES to.
	*/
	function getReward (
		address _to
	) external nonReentrant {
		// Only allow a user to call this function once per block.
		require(block.timestamp > lastRewardTime[_to], "Cannot call getReward twice in the same block.");

		(
			uint256 reward,
			uint256 daoCommision
		) = IStaker(STAKER).claimReward(_to);

		// Only allow a certain amount of tokens to be minted per call.
		require(reward <= maxReward, "Reward amount too high.");

		// Mint both reward BYTES and the DAO tax to targeted recipients.
		if (reward > 0) {
			_mint(_to, reward);
		}
		if (daoCommision > 0) {
			_mint(TREASURY, daoCommision);
		}

		// Update the last reward time for this user.
		lastRewardTime[_to] = block.timestamp;
	}

  ...
}
```
# 2. Potential integer overflow in BYTES2 contract's `burn` and NeoTokyoStaker contract's `_stakeLP` function
### Links : https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L140
### https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1124
### Summary: 
The BYTES2 contract includes a `burn` function that allows authorized callers to burn BYTES tokens from an address, with 2/3 of the burned tokens being minted to the DAO treasury. However, the function multiplies the amount of tokens burned by 2/3 without checking for potential integer overflow, which could result in incorrect calculations and unexpected behavior.
### Impact: 
An attacker could potentially exploit this vulnerability to burn an excessively large amount of tokens and mint an unexpected amount to the DAO treasury, leading to unexpected behavior and potential economic loss.
### Recommendation: 
To prevent potential integer overflow and improve the security of the contract, it is recommended to include a check for potential overflow before executing the calculation. This can be achieved by using a safe math library or by checking if the result of the multiplication operation is less than or equal to the maximum possible value for a uint256.
On the other hand, the `stakeLP` function also contains the same vulnerability. The `_stakeLP` function has a potential integer overflow vulnerability when calculating `points` variable. This can lead to incorrect staking rewards and possible exploit by attackers.

Example: To address the issue, you can modify the `burn` function as follows:
```
function burn(address _from, uint256 _amount) hasValidPermit(UNIVERSAL, BURN) external {
    _burn(_from, _amount);

    uint256 treasuryShare = _amount.mul(2).div(3);
    require(treasuryShare <= type(uint256).max, "Potential integer overflow");
    _mint(TREASURY, treasuryShare);
}
```
In this modified version of the function, I use the `mul` and `div` functions of the SafeMath library to perform the calculation, and check for potential integer overflow using a `require` statement. Alternatively, we can check if the result of the multiplication is less than or equal to `type(uint256).max` using an `if` statement.
# 3. Array Out-of-Bounds Read in `getStakerPositions` Function
### Link: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L710
### Summary: 
The `getStakerPositions` function retrieves the staking positions of a particular staker by compiling details from two arrays `_stakerS1Position` and `_stakerS2Position`. The function uses a loop to iterate through each element in these arrays and assigns the corresponding staker details to new arrays `stakedS1Details` and `stakedS2Details`, respectively. However, the loop indices i for both loops are not explicitly initialized, which can lead to an array out-of-bounds read vulnerability.
### Impact: 
An attacker could exploit this vulnerability to cause the function to read memory outside of the intended range, which may result in a runtime error or unexpected behavior that could potentially compromise the security of the system.
### Recommendation:
1.	Initialize the loop indices: Initialize the loop indices `i` for each loop before using them to access elements of the corresponding array.(`i`==>`j`) 
2.	Use SafeMath: Use the `SafeMath` library to perform arithmetic operations on loop indices and array lengths, to prevent potential integer overflow or underflow issues.
# 4. Double Staking Vulnerability in `_stakeS2Citizen` Function
### Link: https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L995
### Summary: 
The `_stakeS2Citizen` function allows users to stake their S2 Citizen tokens in the staking contract. However, there is a vulnerability that allows users to stake the same S2 Citizen token twice, resulting in double staking and potential reward exploitation.
### Impact: 
An attacker can exploit this vulnerability to earn more rewards than they are entitled to. If an attacker double stakes their S2 Citizen token, they will receive rewards for both stakes, leading to an unfair advantage over other stakers. The impact can be severe, especially if the staking contract has a significant amount of funds or assets.
### Recommendation: 
A possible solution to fix this vulnerability is to add a check to ensure that the S2 Citizen token has not been staked before. One way to do this is to keep track of all staked S2 Citizen tokens using a mapping or a set data structure. Before staking the S2 Citizen token, the function should check if the token has been staked before. If the token has been staked, the function should revert the transaction.

# 5 . Arithmetic Overflow in `getPoolReward` Function
### Link : https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1264
To avoid the possibility of an arithmetic overflow, I recommend the use of `SafeMath` library for all arithmetic operations in the contract. SafeMath will automatically check for overflows and underflows before executing the arithmetic operation

