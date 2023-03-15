# Summary
## Quality Assurance Findings List:
| Number |Issues Details|
|:--:|:-------|
|[QA-1]| Lack of check of zero values in staking and withdrawing of LP and staking of Bytes |
|[QA-2]| Adding user based total points |
|[QA-3]| Use array inputs for stake and withdraw |
|[QA-4]| Unecessary sanity check for argument |
|[QA-5]| Active pool should be inclusive for the time it starts at |

### [QA-1] Lack of check of zero values in staking and withdrawing of LP and staking of Bytes

when staking and withdrawing LP or staking Bytes, it's a good practice to exclude any 0 amount.

```solidity
    error ZeroValue();
    function _stakeBytes(uint256) private {
        uint256 amount;
        uint256 citizenId;
        uint256 seasonId;
        assembly {
            amount := calldataload(0x44)
            citizenId := calldataload(0x64)
            seasonId := calldataload(0x84)
        }
        if(amount == 0) revert ZeroValue();
        // ...
    }

    function _stakeLP(uint256 _timelock) private {
        uint256 amount;
        assembly {
            amount := calldataload(0x44)
        }
        if(amount == 0) revert ZeroValue();
        // ...
    }

    function _withdrawLP() private {
        uint256 amount;
        assembly {
            amount := calldataload(0x24)
        }
        if(amount == 0) revert ZeroValue();
        // ...
    }
```


### [QA-2] Adding user based pool total points

In getPoolReward, there is an excessive iteration through the tokens staked to collect the points, it could be drastically improved, if a simple userTotalPoints for S1 and S2 pools could help to easily figure out the user based total points.

```diff 
+mapping(address => uint256) public s1TotalPoints;

+mapping(address => uint256) public s2TotalPoints;

    function _stakeS1Citizen(uint256 _timelock) private {
        uint256 citizenId;
        uint256 vaultId;
        uint256 handClaimant;
    // ...
    unchecked {
            citizenStatus.points = identityPoints * vaultMultiplier * timelockMultiplier / _DIVISOR / _DIVISOR;
+            s1TotalPoints[msg.sender] += identityPoints * vaultMultiplier * timelockMultiplier / _DIVISOR / _DIVISOR;
            citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

            // Record the caller's staked S1 Citizen.
            _stakerS1Position[msg.sender].push(citizenId);

            // Update the pool point weights for rewards
            pool.totalPoints += citizenStatus.points;
        }

        // Emit an event recording this S1 Citizen staking.
        emit Stake(msg.sender, S1_CITIZEN, _timelock, citizenId);
    }

    function _stakeS2Citizen(uint256 _timelock) private {
        uint256 citizenId;

        // Extract the S2 Citizen ID from the calldata.
        assembly {
            citizenId := calldataload(0x44)
        }
        // ...
        unchecked {
            citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
+            s2TotalPoints[msg.sender] += 100 * timelockMultiplier / _DIVISOR;
            citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

            // Record the caller's staked S2 Citizen.
            _stakerS2Position[msg.sender].push(citizenId);

            // Update the pool point weights for rewards
            pool.totalPoints += citizenStatus.points;
        }

        // Emit an event recording this S1 Citizen staking.
        emit Stake(msg.sender, S2_CITIZEN, _timelock, citizenId);
    }

    function _stakeBytes(uint256) private {
        // ...
        if (seasonId == 1) {
            // ...
            unchecked {
                uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
                citizenStatus.stakedBytes += amount;
                citizenStatus.points += bonusPoints;
+                s1TotalPoints[msg.sender] += bonusPoints;
                pool.totalPoints += bonusPoints;
            }
        } else if (seasonId == 2) {
            // ...
            unchecked {
                // investigate, stake 1 -> 0 bonusPoints
                uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
                citizenStatus.stakedBytes += amount;
                citizenStatus.points += bonusPoints;
+                s2TotalPoints[msg.sender] += bonusPoints;
                pool.totalPoints += bonusPoints;
            }
        } else {
            revert InvalidSeasonId(seasonId);
        }
        // ...
    }

    function _withdrawS1Citizen() private {
        uint256 citizenId;
        assembly {
            citizenId := calldataload(0x24)
        }
        // ...
        unchecked {
            pool.totalPoints -= stakedCitizen.points;
+           s1TotalPoints[msg.sender] -= stakedCitizen.points;
        }
        // ...
    }

    
    function _withdrawS2Citizen() private {
        uint256 citizenId;
        assembly {
            citizenId := calldataload(0x24)
        }
        // ...
        PoolData storage pool = _pools[AssetType.S2_CITIZEN];
        unchecked {
            pool.totalPoints -= stakedCitizen.points;
+           s2TotalPoints[msg.sender] -= stakedCitizen.points;
        }
        // ...
    }

    function getPoolReward(AssetType _assetType, address _recipient) public view returns (uint256, uint256) {
        // ...
        PoolData storage pool = _pools[_assetType];
        if (pool.totalPoints != 0) {
            // Calculate the total number of points accrued to the `_recipient`.
            uint256 points;
            if (_assetType == AssetType.S1_CITIZEN) {
+                points = s1TotalPoints[_recipient];
-                for (uint256 i; i < _stakerS1Position[_recipient].length;) {
-                    uint256 citizenId = _stakerS1Position[_recipient][i];
-                    StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
-                    unchecked {
-                        points += s1Citizen.points;
-                        i++;
-                    }
-                }
            } else if (_assetType == AssetType.S2_CITIZEN) {
+                points = s2TotalPoints[_recipient];
-                for (uint256 i; i < _stakerS2Position[_recipient].length;) {
-                    uint256 citizenId = _stakerS2Position[_recipient][i];
-                    StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];
-                    unchecked {
-                        points += s2Citizen.points;
-                        i++;
-                    }
-                }
            } else if (_assetType == AssetType.LP) {
                unchecked {
                    points += stakerLPPosition[_recipient].points;
                }
            } else {
                revert InvalidAssetType(uint256(_assetType));
            }
        }
        return (0, 0);
    }

```

### [QA-3] Use array inputs for stake and withdraw

- To improve on UX, and allow for atomic staking, it's best if stake function allow for an array of `AssetType[] _assetTypes`, and `bytes[] calldata _data`.

- Note that the following have been written on notepad without any compiler checks for errors, so it might have some errors, the main point is to convey the implementation.

```solidity
    function stake(AssetType[] _assetTypes, bytes[] calldata _datas) external nonReentrant {
        // ...
        uint256 len = _datas.length;
        uint256 _assetType;
        uint256 _timelock;
        for(uint i; i < len; ++i) {
            _assetType = _assetTypes[i];

            // Validate that the asset being staked matches a configured pool.
            if (_pools[_assetType].rewardCount == 0) {
                revert UnconfiguredPool(uint256(_assetType));
            }

            // Validate that the asset being staked matches an active pool.
            if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
                revert InactivePool(uint256(_assetType));
            }

            if(_assetType == AssetType.S1_CITIZEN) {
                uint256 _citizenId;
                uint256 _vaultId;
                uint256 _handClaimant;
                (_timelock, _citizenId, _vaultId, _handClaimant) = abi.decode(_datas[i],(uint256,uint256,uint256,uint256));
                _checkTimelockOption(_assetType, _timelock);
                _stakeS1Citizen(_timelock, _citizenId, _vaultId, _handClaimant);
            } else if(_assetType == AssetType.S2_CITIZEN) {
                uint256 _citizenId;
                (_timelock, _citizenId) = abi.decode(_datas[i],(uint256,uint256));
                _checkTimelockOption(_assetType, _timelock);
                _stakeS2Citizen(_timelock, _citizenId);
            } else if(_assetType == AssetType.BYTES) {
                uint256 amount;
                uint256 citizenId;
                uint256 seasonId;
                (_timelock, _amount, _citizenId, _seasonId) = abi.decode(_datas[i],(uint256,uint256,uint256,uint256));
                _stakeBytes(_amount, _citizenId, _seasonId);
            // compiler ensures that _assetType has max value of 3
            } else {
                uint256 _amount;
                (_timelock, _amount) = abi.decode(_datas[i],(uint256,uint256));
                _checkTimelockOption(_assetType, _timelock);
                _stakeLP(_timelock, _amount);
            }
        }
    }

    function _checkTimelockOption(AssetType _assetType, uint256 _timelockId) private {
        if (timelockOptions[_assetType][_timelockId] == 0) {
            revert InvalidTimelockOption(uint256(_assetType), _timelockId);
        }
        // Grant the caller their total rewards with each staking action.
        IByteContract(BYTES).getReward(msg.sender);
    }

    function _stakeS1Citizen(uint256 _timelock, uint256 _citizenId, uint256 _vaultId, uint256 _handClaimant) private {
        //...
    }

    function _stakeS2Citizen(uint256 _timelock, uint256 _citizenId) private {
        //...
    }

    function _stakeBytes(uint256 _amount, uint256 _citizenId, uint256 _seasonId) private {
        //...
    }

    function _stakeLP(uint256 _timelock, uint256 _amount) private {
        //...
    }
```

### [QA-4] Unecessary sanity check for argument

- There are unecessary sanity checks, in the case of enum argument, the compiler automatically ensures that the value being inputted is a valid value, the following checks are not needed:

```diff
    function stake(AssetType _assetType, uint256 _timelockId, uint256, uint256, uint256) external nonReentrant {
-        if (uint8(_assetType) > 4) {
-            revert InvalidAssetType(uint256(_assetType));
-        }
        // ...
    }

    function withdraw(AssetType _assetType, uint256) external nonReentrant {
+        if (uint8(_assetType) == 2) {
-        if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
            revert InvalidAssetType(uint256(_assetType));
        }
        // ...
    }

```

### [QA-5] Active pool should be inclusive for the time it starts at

- In stake function, the check to ensure the first reward window had started doesn't include the block.timestamp, since it starts at it, it should include it:

```diff
    function stake(AssetType _assetType, uint256 _timelockId, uint256, uint256, uint256) external nonReentrant {
        // ...
        // Validate that the asset being staked matches an active pool.
        // tbd QA, start time got to be inclusive for the time it starts at
+        if (_pools[_assetType].rewardWindows[0].startTime > block.timestamp) {
-        if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
            revert InactivePool(uint256(_assetType));
        }
    }
```