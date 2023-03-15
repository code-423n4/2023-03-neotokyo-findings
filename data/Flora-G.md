## **\[G-1\] Unnecessary code used in for loop**  
This block of code can be outside of the second for loop: [NeoTokyoStaker.sol#L1329-L1337](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1329-L1337)  
Change from

```solidity
                if (lastPoolRewardTime < window.startTime) {
                    uint256 currentRewardRate = pool
                        .rewardWindows[i - 1]
                        .reward;
                    /*
                        Iterate forward to the present timestamp over any unclaimed reward 
                        windows.
                    */
                    for (uint256 j = i; j < windowCount; ) {
                        // If the current time falls within this window, complete.
                        if (block.timestamp <= window.startTime) {
                            unchecked {
                                uint256 timeSinceReward = block.timestamp -
                                    lastPoolRewardTime;
                                totalReward +=
                                    currentRewardRate *
                                    timeSinceReward;
                            }

                            // We have no forward goto and thus include this bastardry.
                            i = windowCount;
                            break;

                            // Otherwise, accrue the remainder of this window and iterate.
                        } else {
                            unchecked {
                                uint256 timeSinceReward = window.startTime -
                                    lastPoolRewardTime;

                                totalReward +=
                                    currentRewardRate *
                                    timeSinceReward;
                            }
                            currentRewardRate = window.reward;
                            lastPoolRewardTime = window.startTime;

                            /*
                                Handle the special case of overrunning the final window by 
                                fulfilling the prior window and then jumping forward to use the 
                                final reward window.
                            */
                            if (j == windowCount - 1) {
                                unchecked {
                                    uint256 timeSinceReward = block.timestamp -
                                        lastPoolRewardTime;
                                    totalReward +=
                                        currentRewardRate *
                                        timeSinceReward;
                                }

                                // We have no forward goto and thus include this bastardry.
                                i = windowCount;
                                break;

                                // Otherwise, iterate.
                            } else {
                                window = pool.rewardWindows[j + 1];
                            }...
                    
```

To 

```solidity
                if (lastPoolRewardTime < window.startTime) {
                    uint256 currentRewardRate = pool
                        .rewardWindows[i - 1]
                        .reward;
                    if (block.timestamp <= window.startTime) {
                        unchecked {
                            uint256 timeSinceReward = block.timestamp -
                                lastPoolRewardTime;
                            totalReward += currentRewardRate * timeSinceReward;
                        }

                        // We have no forward goto and thus include this bastardry.
                        break;

                        // Otherwise, accrue the remainder of this window and iterate.
                    }
                    /*
                        Iterate forward to the present timestamp over any unclaimed reward 
                        windows.
                    */
                    for (uint256 j = i; j < windowCount; ) {
                        // If the current time falls within this window, complete.
                        unchecked {
                            uint256 timeSinceReward = window.startTime -
                                lastPoolRewardTime;

                            totalReward += currentRewardRate * timeSinceReward;
                        }
                        currentRewardRate = window.reward;
                        lastPoolRewardTime = window.startTime;

                        /*
                                Handle the special case of overrunning the final window by 
                                fulfilling the prior window and then jumping forward to use the 
                                final reward window.
                            */
                        if (j == windowCount - 1) {
                            unchecked {
                                uint256 timeSinceReward = block.timestamp -
                                    lastPoolRewardTime;
                                totalReward +=
                                    currentRewardRate *
                                    timeSinceReward;
                            }

                            // We have no forward goto and thus include this bastardry.
                            i = windowCount;
                            break;

                            // Otherwise, iterate.
                        } else {
                            window = pool.rewardWindows[j + 1];
                        }...
```

## **\[G-2\]** **Before some functions, we should check arguments for possible gas save.**  
For example, we should check for amount being 0 so the function does not run when its not gonna do anything:

Check \_amount not equal to 0:  
[BYTES2.sol#L93-L95](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L93-L95)

```solidity
	function upgradeBytes (
		uint256 _amount
	) external {
```

[NeoTokyoStaker.sol#L799](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L799)

```solidity
	function _assetTransfer (
		address _asset,
		address _to,
		uint256 _amount
	) private {
```

Check address `_to` not equal to address(0)  
[BYTES2.sol#L114-L116](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114-L116)

```solidity
	function getReward (
		address _to
	) external {
```

## **\[G-3\] += and -= costs more gas than = + and = - for state variables**

`+` operation   
[NeoTokyoStaker.sol#L977](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L977)

```solidity
			pool.totalPoints += citizenStatus.points;
```

[NeoTokyoStaker.sol#L1029](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1029)

```solidity
			pool.totalPoints += citizenStatus.points;
```

[NeoTokyoStaker.sol#L1078-L1080](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1078-L1080) 

```solidity
				citizenStatus.stakedBytes += amount;
				citizenStatus.points += bonusPoints;
				pool.totalPoints += bonusPoints;
```

[NeoTokyoStaker.sol#L1099-L1101](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1099-L1101)

```solidity
				citizenStatus.stakedBytes += amount;
				citizenStatus.points += bonusPoints;
				pool.totalPoints += bonusPoints;
```

[NeoTokyoStaker.sol#L1160-L1164](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1160-L1164)

```solidity
			stakerLPPosition[msg.sender].amount += amount;
			stakerLPPosition[msg.sender].points += points;

			// Update the pool point weights for rewards.
			pool.totalPoints += points;
```

`-` operation

[NeoTokyoStaker.sol#L1580](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1580)

```solidity
			pool.totalPoints -= stakedCitizen.points;
```

[NeoTokyoStaker.sol#L1626-L1630](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1626-L1630)  

```solidity
			lpPosition.amount -= amount;
			lpPosition.points -= points;

			// Update the pool point weights for rewards.
			pool.totalPoints -= points;
```

## **\[G-4\] Using delete instead of setting struct 0 saves gas**  
[NeoTokyoStaker.sol#L1517-L1521](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517-L1521)

```solidity
		stakedCitizen.stakedBytes = 0;
		stakedCitizen.timelockEndTime = 0;
		stakedCitizen.points = 0;
		stakedCitizen.hasVault = false;
		stakedCitizen.stakedVaultId = 0;
```

[NeoTokyoStaker.sol#L1582-L1584](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1582-L1584)

```solidity
		stakedCitizen.stakedBytes = 0;
		stakedCitizen.timelockEndTime = 0;
		stakedCitizen.points = 0;
```

## **\[G-5\] Use double if statements instead of &&**  
If the if statement has a logical AND and is not followed by an else statement, it can be replaced with 2 if statements.  
[NeoTokyoStaker.sol#L910](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L910)

```solidity
		if (citizenVaultId != 0 && vaultId != 0) {
```

[NeoTokyoStaker.sol#L917](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L917)

```solidity
		} else if (citizenVaultId != 0 && vaultId == 0) {
```

[NeoTokyoStaker.sol#L926](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L926)

```solidity
		} else if (citizenVaultId == 0 && vaultId != 0) {
```

[NeoTokyoStaker.sol#L1834](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1834)

```solidity
		if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
```

## **\[G-6\] Setting the constructor to payable**  
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks.  
[BYTES2.sol#L75-L85](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-L85)

```solidity
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

  
[NeoTokyoStaker.sol#L588-L597](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L597)

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
```

## **\[G-7\] Functions guaranteed to revert\_ when called by normal users can be marked payable**  
If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.  
[BYTES2.sol#L140-L143](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L140-L143)

```solidity
	function burn (
		address _from,
		uint256 _amount
	) hasValidPermit(UNIVERSAL, BURN) external {
```

## **\[G-8\] Instead of address(this), it is more gas-efficient to pre-calculate and use the address with a hardcode.**  
Foundry's script.sol and solmate LibRlp.sol contracts can do this => Reference: [Here](https://book.getfoundry.sh/reference/forge-std/compute-create-address) and [here](https://twitter.com/transmissions11/status/1518507047943245824)

[NeoTokyoStaker.sol#L897](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L897)

```solidity
		_assetTransferFrom(S1_CITIZEN, msg.sender, address(this), citizenId);
```

[NeoTokyoStaker.sol#L927](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L927)

```solidity
 			_assetTransferFrom(VAULT, msg.sender, address(this), vaultId);
```

[NeoTokyoStaker.sol#L1010](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1010)

```solidity
 		_assetTransferFrom(S2_CITIZEN, msg.sender, address(this), citizenId);
```

[NeoTokyoStaker.sol#L1137](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1137)

```solidity
		_assetTransferFrom(LP, msg.sender, address(this), amount);
```

[NeoTokyoStaker.sol#L1057](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1057) 

```solidity
		_assetTransferFrom(BYTES, msg.sender, address(this), amount);
```

[NeoTokyoStaker.sol#L1485](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1485)

```solidity
		if (stakedCitizen.stakedVaultId != 0) {
			_assetTransferFrom(
				VAULT,
				address(this),
				msg.sender,
				stakedCitizen.stakedVaultId
			);
		}
```

[NeoTokyoStaker.sol#L1492](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1492)

```solidity
		_assetTransferFrom(S1_CITIZEN, address(this), msg.sender, citizenId);
```

  
[NeoTokyoStaker.sol#L1557](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1557)

```solidity
		_assetTransferFrom(S2_CITIZEN, address(this), msg.sender, citizenId);
```

## **\[G-9\] Preliminarily save length of the array to a local variable and use this variable as a loop condition to save gas**

[NeoTokyoStaker.sol#L716-L717](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L716-L717)

```solidity
			new StakedS1CitizenOutput[](_stakerS1Position[_staker].length);
		for (uint256 i; i < _stakerS1Position[_staker].length; ) {
```

  
[NeoTokyoStaker.sol#L733-L734](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L733-L734)

```solidity
			new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
		for (uint256 i; i < _stakerS2Position[_staker].length; ) {
```

  
[NeoTokyoStaker.sol#L1279](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1279)

```solidity
			for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
```

  
[NeoTokyoStaker.sol#L1288](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1288)

```solidity
			for (uint256 i; i < _stakerS2Position[_recipient].length; ) {
```

  
[NeoTokyoStaker.sol#L1499](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1499)

```solidity
			for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
```

## **\[G-10\] Use** `=` **to update points variable instead of** `+=`

[NeoTokyoStaker.sol#L1298](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1298) 

Change 

```solidity
			points += stakerLPPosition[_recipient].points;
```

To

```solidity
			points = stakerLPPosition[_recipient].points
```

## **\[G-11\] Set the optimization value higher than 800 in your hardhat.config.ts file to optimize for run-time gas costs (when functions are called on a contract).**

[hardhat.config.js#L28-L51](https://github.com/code-423n4/2023-03-neotokyo/blob/main/hardhat.config.js#L28-L51)

```solidity
			{
				version: '0.8.11',
				settings: {
					optimizer: {
						enabled: true,
						runs: 200, 
						details: {
							yul: true 
						}
					}
				}
			},
			{
				version: '0.8.19',
				settings: {
					optimizer: {
						enabled: true,
						runs: 200, 
						details: {
							yul: true 
						}
					}
				}
			}
```

## **\[G-12\] Since the values of lastRewardTime mapping are set the same regardless of the keys, consider changing it to a uint256 variable**

[NeoTokyoStaker.sol#L319](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L319)

Change 

```solidity
mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
```

To

```solidity
uint256 public lastRewardTime;
```