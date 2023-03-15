## 1. `keccak256()` EXPRESSIONS WHICH ARE FIXED, CAN BE PRECALCULATED AND ASSIGNED TO THE CONSTANT VARIABLES.

Instead of calculating the keccak256() when the contract is made, pre calculate them before and only give the result to a constant

	/// The identifier for the right to configure the LP token address.
	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

	/// The identifier for the right to configure timelock options.
	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
		"CONFIGURE_TIMELOCKS"
	);

	/// The identifier for the right to configure Identity and Vault points.
	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

	/// The identifier for the right to configure emission rates and the DAO tax.
	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

	/// The identifier for the right to configure BYTES staking caps.
	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS"); 

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L205-L220
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L36-L40

## 2. `storage array` IN THE `for` LOOP CAN BE CACHED AND READ FROM MEMORY

In the `getStakerPositions` function in the `NeoTokyoStaker` contract, a for loop is used to retrieve the position of the each staked asset of a particular staker.

		for (uint256 i; i < _stakerS1Position[_staker].length; ) {
			uint256 citizenId = _stakerS1Position[_staker][i];

But here the `uint256[]` `storage array` is used to loop through, which costs high gas since reading from the storage. Instead the storage array can be cached into memory before the `for` loop and the resultant memory array can be used inside the `for` loop.
Since the array details are only used for read only inside the function, this will not effect the functionality of the operation.

The update code will look as below:

		uint256[] cachedArray = _stakerS1Position[_staker][i];	
		for (uint256 i; i < _stakerS1Position[_staker].length; ) {
			uint256 citizenId = cachedArray[i];	
			
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L717-L718
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L734-L735

## 3. NO NEED TO CALL THE `+=` AND ONLY `=` ASSIGNMENT IS ENOUGH FOR `points` CALCULATION

In the `getPoolReward` function of the `NeoTokyoStaker` contract, `points` for the `_recipient` for staked LP position are calculated as follows:

					points += stakerLPPosition[_recipient].points;
					
Here no need to use the `+=` operation since it is only an assignment and `points` variable is not added iteratively via a `for` loop as it is with the `S1_CITIZEN` and `S2_CITIZEN` `points` calculations.

Hence the `points` calculations for the staked LP position can be calculated as below to save gas.

					points = stakerLPPosition[_recipient].points;
					
Here only the variable assignment will be performed. Hence gas will be saved since no addition operation is performed.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1298 

## 4. CAN MAKE THE VARIABLE OUTSIDE THE `for` LOOP TO SAVE GAS

Consider making the stack variables before the `for` loop which will save gas. As per the below depicted code snippet, the variables of types `RewardWindow` and `uint256` can be declared outside the `for` loop to save gas.

			for (uint256 i; i < windowCount; ) {
				RewardWindow memory window = pool.rewardWindows[i];

				/*
					If the last reward time is less than the starting time of this 
					window, then the reward was accrued in the previous window.
				*/
				if (lastPoolRewardTime < window.startTime) {
					uint256 currentRewardRate = pool.rewardWindows[i - 1].reward; 
				
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1312-L1313
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1320
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1331
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1342
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1355-L1356
