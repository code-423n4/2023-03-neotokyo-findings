## Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

2023-03-neotokyo/https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::2 => pragma solidity ^0.8.19;
2023-03-neotokyo/https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::2 => pragma solidity ^0.8.19;

## Variable names too similar

Variables with names that are too similar.

Variable NeoTokyoStaker.stakerS1Position (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#378) is too similar to NeoTokyoStaker.stakerS2Position (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#411)
Variable NeoTokyoStaker.getStakerPositions(address).stakedS1Details (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#715-716) is too similar to NeoTokyoStaker.getStakerPositions(address).stakedS2Details (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#732-733)
Variable NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address).timeSinceReward_scope_3 (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1342) is too similar to NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address).timeSinceReward_scope_4 (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1355-1356)
Variable NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address).timeSinceReward_scope_3 (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1342) is too similar to NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address).timeSinceReward_scope_5 (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1378)
Variable NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address).timeSinceReward_scope_4 (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1355-1356) is too similar to NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address).timeSinceReward_scope_5 (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1378)

## Conformance to Solidity naming conventions

Solidity defines a [naming convention](https://solidity.readthedocs.io/en/v0.4.25/style-guide.html#naming-conventions) that should be followed.

**Recommendation**: Follow the Solidity naming convention

Parameter BYTES2.upgradeBytes(uint256).amount (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#94) is not in mixedCase
Parameter BYTES2.getReward(address).to (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#115) is not in mixedCase
Parameter BYTES2.burn(address,uint256).from (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#141) is not in mixedCase
Parameter BYTES2.burn(address,uint256).amount (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#142) is not in mixedCase
Parameter BYTES2.changeStakingContractAddress(address).staker (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#163) is not in mixedCase
Parameter BYTES2.changeTreasuryContractAddress(address).treasury (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#174) is not in mixedCase
Variable BYTES2.BYTES1 (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#43) is not in mixedCase
Variable BYTES2.S1_CITIZEN (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#46) is not in mixedCase
Variable BYTES2.STAKER (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#49) is not in mixedCase
Variable BYTES2.TREASURY (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#52) is not in mixedCase
Parameter NeoTokyoStaker.getCreditYield(uint256,uint256).citizenId (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#626) is not in mixedCase
Parameter NeoTokyoStaker.getCreditYield(uint256,uint256).vaultId (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#627) is not in mixedCase
Parameter NeoTokyoStaker.getConfiguredVaultMultiplier(uint256).vaultId (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#660) is not in mixedCase
Parameter NeoTokyoStaker.getStakerPosition(address,NeoTokyoStaker.AssetType).staker (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#690) is not in mixedCase
Parameter NeoTokyoStaker.getStakerPosition(address,NeoTokyoStaker.AssetType).assetType (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#691) is not in mixedCase
Parameter NeoTokyoStaker.getStakerPositions(address).staker (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#711) is not in mixedCase
Parameter NeoTokyoStaker.stake(NeoTokyoStaker.AssetType,uint256,uint256,uint256,uint256).assetType (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1197) is not in mixedCase
Parameter NeoTokyoStaker.stake(NeoTokyoStaker.AssetType,uint256,uint256,uint256,uint256).timelockId (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1198) is not in mixedCase
Parameter NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address).assetType (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1265) is not in mixedCase
Parameter NeoTokyoStaker.getPoolReward(NeoTokyoStaker.AssetType,address).recipient (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1266) is not in mixedCase
Parameter NeoTokyoStaker.claimReward(address).recipient (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1410) is not in mixedCase
Parameter NeoTokyoStaker.withdraw(NeoTokyoStaker.AssetType,uint256).assetType (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1660) is not in mixedCase
Parameter NeoTokyoStaker.configureLP(address).lp (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1709) is not in mixedCase
Parameter NeoTokyoStaker.configureTimelockOptions(NeoTokyoStaker.AssetType,uint256[],uint256[]).assetType (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1738) is not in mixedCase
Parameter NeoTokyoStaker.configureTimelockOptions(NeoTokyoStaker.AssetType,uint256[],uint256[]).timelockIds (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1739) is not in mixedCase
Parameter NeoTokyoStaker.configureTimelockOptions(NeoTokyoStaker.AssetType,uint256[],uint256[]).encodedSettings (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1740) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditYields(uint256[],string[],string[]).citizenRewardRates (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1761) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditYields(uint256[],string[],string[]).vaultRewardRates (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1762) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditYields(uint256[],string[],string[]).identityCreditYields (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1763) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditPoints(string[],uint256[]).identityCreditYields (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1784) is not in mixedCase
Parameter NeoTokyoStaker.configureIdentityCreditPoints(string[],uint256[]).points (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1785) is not in mixedCase
Parameter NeoTokyoStaker.configureVaultCreditMultipliers(string[],uint256[]).vaultCreditMultipliers (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1803) is not in mixedCase
Parameter NeoTokyoStaker.configureVaultCreditMultipliers(string[],uint256[]).multipliers (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1804) is not in mixedCase
Parameter NeoTokyoStaker.configurePools(NeoTokyoStaker.PoolConfigurationInput[]).inputs (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1820) is not in mixedCase
Parameter NeoTokyoStaker.configureCaps(uint256,uint256).vaultedCap (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1852) is not in mixedCase
Parameter NeoTokyoStaker.configureCaps(uint256,uint256).unvaultedCap (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1853) is not in mixedCase
Variable NeoTokyoStaker.BYTES (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#223) is not in mixedCase
Variable NeoTokyoStaker.S1_CITIZEN (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#226) is not in mixedCase
Variable NeoTokyoStaker.S2_CITIZEN (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#229) is not in mixedCase
Variable NeoTokyoStaker.LP (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#232) is not in mixedCase
Variable NeoTokyoStaker.IDENTITY (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#239) is not in mixedCase
Variable NeoTokyoStaker.VAULT (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#242) is not in mixedCase
Variable NeoTokyoStaker.VAULT_CAP (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#249) is not in mixedCase
Variable NeoTokyoStaker.NO_VAULT_CAP (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#255) is not in mixedCase

## Unused Code

Remove unused functions.

NeoTokyoStaker.assetTransfer(address,address,uint256) (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#796-814) is never used and should be removed
NeoTokyoStaker.assetTransferFrom(address,address,address,uint256) (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#766-786) is never used and should be removed
NeoTokyoStaker.stakeBytes(uint256) (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1044-1116) is never used and should be removed
NeoTokyoStaker.stakeLP(uint256) (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1124-1174) is never used and should be removed
NeoTokyoStaker.stakeS1Citizen(uint256) (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#875-987) is never used and should be removed
NeoTokyoStaker.stakeS2Citizen(uint256) (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#995-1039) is never used and should be removed
NeoTokyoStaker.stringEquals(string,string) (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#824-865) is never used and should be removed
NeoTokyoStaker.withdrawLP() (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1597-1644) is never used and should be removed
NeoTokyoStaker.withdrawS1Citizen() (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1459-1529) is never used and should be removed
NeoTokyoStaker.withdrawS2Citizen() (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1534-1592) is never used and should be removed

## Missing zero address validation

Check that the address is not zero.

BYTES2.constructor(address,address,address,address).bytes (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#76) lacks a zero-check on : - BYTES1 = bytes (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#81)
BYTES2.constructor(address,address,address,address).s1Citizen (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#77) lacks a zero-check on : - S1_CITIZEN = s1Citizen (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#82)
BYTES2.constructor(address,address,address,address).staker (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#78) lacks a zero-check on : - STAKER = staker (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#83)
BYTES2.constructor(address,address,address,address).treasury (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#79) lacks a zero-check on : - TREASURY = treasury (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#84)
BYTES2.changeStakingContractAddress(address).staker (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#163) lacks a zero-check on : - STAKER = staker (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#165)
BYTES2.changeTreasuryContractAddress(address).treasury (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#174) lacks a zero-check on : - TREASURY = treasury (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#176)
NeoTokyoStaker.constructor(address,address,address,address,address,address,uint256,uint256).bytes (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#589) lacks a zero-check on : - BYTES = bytes (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#598)
NeoTokyoStaker.constructor(address,address,address,address,address,address,uint256,uint256).s1Citizen (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#590) lacks a zero-check on : - S1_CITIZEN = s1Citizen (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#599)
NeoTokyoS## Use shift Right/Left instead of division/multiplication if possible

Shifting left by N is like multiplying by 2^N and shifting right by N is like dividing by 2^N

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::152 => treasuryShare = amount \* 2 / 3;

## Using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::37 => bytes32 public constant BURN = keccak256("BURN");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::40 => bytes32 public constant ADMIN = keccak256("ADMIN");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::206 => bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::209 => bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::214 => bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::217 => bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::220 => bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

## Use != 0 instead of > 0 for Unsigned Integer Comparison

When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::123 => if (reward > 0) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::126 => if (daoCommision > 0) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1477 => if (stakedCitizen.stakedBytes > 0) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1552 => if (stakedCitizen.stakedBytes > 0) {

## Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::717 => for (uint256 i; i < stakerS1Position[_staker].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::733 => new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::734 => for (uint256 i; i < stakerS2Position[_staker].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::834 => let length := mload(a)
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::835 => switch eq(length, mload(b))
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::837 => // Proceed to compare string contents if lengths are equal.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::843 => let end := add(mc, length)
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::859 => // By default the array length is not equal so the strings are not equal.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1279 => for (uint256 i; i < stakerS1Position[_recipient].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1288 => for (uint256 i; i < stakerS2Position[_recipient].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1499 => for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1503 => if (stakedIndex != oldPosition.length - 1) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1504 => oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1564 => for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1568 => if (stakedIndex != oldPosition.length - 1) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1569 => oldPosition[st## Use shift Right/Left instead of division/multiplication if possible

Shifting left by N is like multiplying by 2^N and shifting right by N is like dividing by 2^N

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::152 => treasuryShare = amount \* 2 / 3;

## Using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::37 => bytes32 public constant BURN = keccak256("BURN");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::40 => bytes32 public constant ADMIN = keccak256("ADMIN");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::206 => bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::209 => bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::214 => bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::217 => bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::220 => bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

## Use != 0 instead of > 0 for Unsigned Integer Comparison

When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::123 => if (reward > 0) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol::126 => if (daoCommision > 0) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1477 => if (stakedCitizen.stakedBytes > 0) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1552 => if (stakedCitizen.stakedBytes > 0) {

## Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::717 => for (uint256 i; i < stakerS1Position[_staker].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::733 => new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::734 => for (uint256 i; i < stakerS2Position[_staker].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::834 => let length := mload(a)
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::835 => switch eq(length, mload(b))
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::837 => // Proceed to compare string contents if lengths are equal.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::843 => let end := add(mc, length)
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::859 => // By default the array length is not equal so the strings are not equal.
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1279 => for (uint256 i; i < stakerS1Position[_recipient].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1288 => for (uint256 i; i < stakerS2Position[_recipient].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1499 => for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1503 => if (stakedIndex != oldPosition.length - 1) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1504 => oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1564 => for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1568 => if (stakedIndex != oldPosition.length - 1) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1569 => oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1742 => for (uint256 i; i < timelockIds.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1765 => for (uint256 i; i < citizenRewardRates.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1787 => for (uint256 i; i < identityCreditYields.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1806 => for (uint256 i; i < vaultCreditMultipliers.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1822 => for (uint256 i; i < inputs.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1823 => uint256 poolRewardWindowCount = inputs[i].rewardWindows.length;
akedIndex] = oldPosition[oldPosition.length - 1];
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1742 => for (uint256 i; i < timelockIds.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1765 => for (uint256 i; i < citizenRewardRates.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1787 => for (uint256 i; i < identityCreditYields.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1806 => for (uint256 i; i < vaultCreditMultipliers.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1822 => for (uint256 i; i < inputs.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol::1823 => uint256 poolRewardWindowCount = inputs[i].rewardWindows.length;
taker.constructor(address,address,address,address,address,address,uint256,uint256).s2Citizen (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#591) lacks a zero-check on : - S2_CITIZEN = s2Citizen (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#600)
NeoTokyoStaker.constructor(address,address,address,address,address,address,uint256,uint256).lpToken (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#592) lacks a zero-check on : - LP = lpToken (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#601)
NeoTokyoStaker.constructor(address,address,address,address,address,address,uint256,uint256).identity (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#593) lacks a zero-check on : - IDENTITY = identity (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#602)
NeoTokyoStaker.constructor(address,address,address,address,address,address,uint256,uint256).vault (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#594) lacks a zero-check on : - VAULT = vault (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#603)
NeoTokyoStaker.configureLP(address).lp (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1709) lacks a zero-check on : - LP = lp (https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#1714)
