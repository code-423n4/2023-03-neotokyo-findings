
## Summary

---

### Low Risk Findings
|Id|Title|Instances|
|:--:|:-------|:--:|
|[L-01]| Hardcoded default `vaultMultiplier` | 1 |
|[L-02]| Check for `assetType` should be `> 3` instead of `> 4` | 1 |
|[L-03]| Missing events in sensitive functions | 9 |

Total: 11 instances over 3 issues.

### Non Critical Findings
|Id|Title|Instances|
|:--:|:-------|:--:|
|[NC-01]| Use of `constant` variables instead of `immutable` | 7 |
|[NC-02]| Use of floating pragma | 2 |
|[NC-03]| Missing or incomplete NatSpec | 5 |

Total: 14 instances over 3 issues.

## Low Risk Findings

---

### [L-01] Hardcoded default `vaultMultiplier`


Use a default key inside `vaultCreditMultiplier` like the other multipliers instead.


*There is 1 instance of this issue.*


```solidity
File: contracts/staking/NeoTokyoStaker.sol

946: 				uint256 vaultMultiplier = 100;
947: 				if (handClaimant == 1) {
948: 					uint256 identityId = citizen.getIdentityIdOfTokenId(citizenId);
949: 					string memory class = IGenericGetter(IDENTITY).getClass(identityId);
950: 					if (_stringEquals(class, "Hand of Citadel")) {
951: 						vaultMultiplier = vaultCreditMultiplier["?"];
952: 					} else {

```

### [L-02] Check for `assetType` should be `> 3` instead of `> 4`


There are only four valid values for `assetType`, not five (`> 4` implies that also id `4` is valid, but only ids `0, 1, 2 ,3` are valid).


*There is 1 instance of this issue.*


```solidity
File: contracts/staking/NeoTokyoStaker.sol

1205: 				if (uint8(_assetType) > 4) {
1668: 				if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {

```

### [L-03] Missing events in sensitive functions

Events should be emitted when sensitive changes are made to the contracts, but some functions lack them.

*There are 9 instances of this issue.*

<details>
<summary>Expand findings</summary>


```solidity
File: contracts/staking/BYTES2.sol

189: 			function updateReward (
190: 				address,
191: 				address,
192: 				uint256
193: 			) external {
194: 			}

204: 			function updateRewardOnMint (
205: 				address,
206: 				uint256
207: 			) external {
208: 			}


File: contracts/staking/NeoTokyoStaker.sol

1708: 			function configureLP (
1709: 				address _lp
1710: 			) external hasValidPermit(UNIVERSAL, CONFIGURE_LP) {
1711: 				if (lpLocked) {
1712: 					revert LockedConfigurationOfLP();
1713: 				}
1714: 				LP = _lp;
1715: 			}

1737: 			function configureTimelockOptions (
1738: 				AssetType _assetType,
1739: 				uint256[] memory _timelockIds,
1740: 				uint256[] memory _encodedSettings
1741: 			) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
1742: 				for (uint256 i; i < _timelockIds.length; ) {
1743: 					timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];
1744: 					unchecked { ++i; }
1745: 				}
1746: 			}

1760: 			function configureIdentityCreditYields (
1761: 				uint256[] memory _citizenRewardRates, 
1762: 				string[] memory _vaultRewardRates,
1763: 				string[] memory _identityCreditYields
1764: 			) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1765: 				for (uint256 i; i < _citizenRewardRates.length; ) {
1766: 					identityCreditYield[
1767: 						_citizenRewardRates[i]
1768: 					][
1769: 						_vaultRewardRates[i]
1770: 					] = _identityCreditYields[i];
1771: 					unchecked { ++i; }
1772: 				}
1773: 			}

1783: 			function configureIdentityCreditPoints (
1784: 				string[] memory _identityCreditYields,
1785: 				uint256[] memory _points
1786: 			) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1787: 				for (uint256 i; i < _identityCreditYields.length; ) {
1788: 					identityCreditPoints[_identityCreditYields[i]] = _points[i];
1789: 					unchecked { ++i; }
1790: 				}
1791: 			}

1802: 			function configureVaultCreditMultipliers (
1803: 				string[] memory _vaultCreditMultipliers,
1804: 				uint256[] memory _multipliers
1805: 			) hasValidPermit(UNIVERSAL, CONFIGURE_CREDITS) external {
1806: 				for (uint256 i; i < _vaultCreditMultipliers.length; ) {
1807: 					vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];
1808: 					unchecked { ++i; }
1809: 				}
1810: 			}

1819: 			function configurePools (
1820: 				PoolConfigurationInput[] memory _inputs
1821: 			) hasValidPermit(UNIVERSAL, CONFIGURE_POOLS) external {
1822: 				for (uint256 i; i < _inputs.length; ) {
1823: 					uint256 poolRewardWindowCount = _inputs[i].rewardWindows.length;
1824: 					_pools[_inputs[i].assetType].rewardCount = poolRewardWindowCount;
1825: 					_pools[_inputs[i].assetType].daoTax = _inputs[i].daoTax;
1826: 		
1827: 					// Set the pool reward window details by populating the mapping.
1828: 					uint256 lastTime;
1829: 					for (uint256 j; j < poolRewardWindowCount; ) {
1830: 						_pools[_inputs[i].assetType].rewardWindows[j] =
1831: 							_inputs[i].rewardWindows[j];
1832: 		
1833: 						// Revert if an invalid pool configuration is supplied.
1834: 						if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
1835: 							revert RewardWindowTimesMustIncrease();
1836: 						}
1837: 						lastTime = _inputs[i].rewardWindows[j].startTime;
1838: 						unchecked { j++; }
1839: 					}
1840: 					unchecked { ++i; }
1841: 				}
1842: 			}

1851: 			function configureCaps (
1852: 				uint256 _vaultedCap,
1853: 				uint256 _unvaultedCap
1854: 			) hasValidPermit(UNIVERSAL, CONFIGURE_CAPS) external {
1855: 				VAULT_CAP = _vaultedCap;
1856: 				NO_VAULT_CAP = _unvaultedCap;
1857: 			}

```
</details>

---
## Non Critical Findings

---

### [NC-01] Use of `constant` variables instead of `immutable`

Constant variables and immutable variables serve different purposes and should be used accordingly.

To clarify, constant variables are used for literal values in the code, whereas immutable variables are ideal for values calculated or passed into the constructor.

*There are 7 instances of this issue.*


```solidity
File: contracts/staking/BYTES2.sol

37: 			bytes32 public constant BURN = keccak256("BURN");

40: 			bytes32 public constant ADMIN = keccak256("ADMIN");


File: contracts/staking/NeoTokyoStaker.sol

206: 			bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");

209: 			bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
210: 				"CONFIGURE_TIMELOCKS"
211: 			);

214: 			bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");

217: 			bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");

220: 			bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```

### [NC-02] Use of floating pragma

Locking the pragma helps avoid accidental deploys with an outdated compiler version that may introduce bugs and unexpected vulnerabilities.

Floating pragma is meant to be used for libraries and contracts that have external users and need backward compatibility.

*There are 2 instances of this issue.*


```solidity
File: contracts/staking/BYTES2.sol

2: 		pragma solidity ^0.8.19;


File: contracts/staking/NeoTokyoStaker.sol

2: 		pragma solidity ^0.8.19;

```

### [NC-03] Missing or incomplete NatSpec

Some functions miss a NatSpec, or they have an incomplete one.
It is recommended that contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI).

Include either `@notice` and/or `@dev` to describe how the function is supposed to work, a `@param` to describe each parameter, and a `@return` for any return values.

*There are 5 instances of this issue.*

<details>
<summary>Expand findings</summary>


```solidity
File: contracts/staking/BYTES2.sol

179: 			/**
180: 				This function is called by the S1 Citizen contract before an NFT transfer 
181: 				and before a call to `getReward`. For historical reasons it must be left 
182: 				here as a stub and cannot be entirely removed, though now it remains as a 
183: 				no-op.
184: 		
185: 				@custom:param A throw-away parameter to fulfill the Citizen call.
186: 				@custom:param A throw-away parameter to fulfill the Citizen call.
187: 				@custom:param A throw-away parameter to fulfill the Citizen call.
188: 			*/
189: 			function updateReward (

196: 			/**
197: 				This function is called by the S1 Citizen contract when a new citizen is 
198: 				minted. For historical reasons it must be left here as a stub and cannot be 
199: 				entirely removed, though now it remains as a no-op.
200: 		
201: 				@custom:param A throw-away parameter to fulfill the Citizen call.
202: 				@custom:param A throw-away parameter to fulfill the Citizen call.
203: 			*/
204: 			function updateRewardOnMint (


File: contracts/staking/NeoTokyoStaker.sol

1176: 			/**
1177: 				Stake a particular asset into this contract, updating its corresponding 
1178: 				rewards.
1179: 		
1180: 				@param _assetType An ID of the specific asset that the caller is attempting 
1181: 					to deposit into this staker.
1182: 				@param _timelockId The ID of a specific timelock period to select. This 
1183: 					timelock ID must be configured for the specific `_assetType`.
1184: 				@custom:param The third parameter is overloaded to have different meaning 
1185: 					depending on the `assetType` selected. In the event of staking an S1 or 
1186: 					S2 Citizen, this parameter is the token ID of the Citizen being staked. 
1187: 					In the event of staking BYTES or LP tokens, this parameter is the amount 
1188: 					of the respective token being staked.
1189: 				@custom:param If the asset being staked is an S1 Citizen, this is the ID of 
1190: 					a Vault to attempt to optionally attach.
1191: 				@custom:param If the asset being staked is an S1 Citizen, this is a flag to 
1192: 					attempt to claim a Hand of the Citadel bonus. If the asset being staked 
1193: 					is BYTES, this is either one or two to select the Neo Tokyo season ID of 
1194: 					the S1 or S2 Citizen that BYTES are being staked into.
1195: 			*/
1196: 			function stake (

1398: 			/**
1399: 				Determine the reward, based on staking participation at this moment, of a 
1400: 				particular recipient. Due to a historic web of Neo Tokyo dependencies, 
1401: 				rewards are actually minted through the BYTES contract.
1402: 		
1403: 				@param _recipient The recipient to calculate the reward for.
1404: 		
1405: 				@return A tuple containing (the number of tokens due to be minted to 
1406: 					`_recipient` as a reward, and the number of tokens that should be minted 
1407: 					to the DAO treasury as a DAO tax).
1408: 			*/
1409: 			function claimReward (

1646: 			/**
1647: 				Withdraw a particular asset from this contract, updating its corresponding 
1648: 				rewards. A caller may only withdraw an asset provided they are the staker 
1649: 				and that timelocks are not violated.
1650: 		
1651: 				@param _assetType An ID of the specific asset that the caller is attempting 
1652: 					to withdraw from this staker.
1653: 				@custom:param The third parameter is overloaded to have different meaning 
1654: 					depending on the `assetType` selected. In the event of withdrawing an S1 
1655: 					or S2 Citizen, this is the token ID of the Citizen to attempt to 
1656: 					withdraw. In the event of withdrawing LP tokens, this is the amount of 
1657: 					the LP token to withdraw.
1658: 			*/
1659: 			function withdraw (

```
</details>

---