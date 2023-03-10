[N1] Could use latest solidity named parameters in mapping types
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

	mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;

	/** 
		This admin-configurable double-mapping allows us to deduce the Identity 
		"Credit Yield" string of a Neo Tokyo S1 Citizen given the Citizen's reward 
		rate and the reward rate of the Citizen's Vault.
	*/
	mapping ( uint256 => mapping ( string => string )) public identityCreditYield;

	/// Assign a configurable multiplier to each S1 Citizen's Identity credit. 
	mapping ( string => uint256 ) public identityCreditPoints;

	/// Assign a configurable multiplier to each S1 Citizen's Vault credit. 
	mapping ( string => uint256 ) public vaultCreditMultiplier;

[N2] Each event should use three indexed fields if there are three or more fields
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol

event Claim (
		address indexed recipient,
		uint256 reward,
		uint256 tax
)

event Withdraw (
		address indexed caller,
		address indexed asset,
		uint256 amountOrTokenId
	)

...

[N3]Division before multiplication incurs unnecessary precision loss
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol
uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;