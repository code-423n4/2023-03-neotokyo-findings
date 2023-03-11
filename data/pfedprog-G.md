## Use shift Right/Left instead of division/multiplication if possible

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
