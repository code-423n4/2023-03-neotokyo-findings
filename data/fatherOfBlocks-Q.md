**NeoTokyoStaker.sol**
- L588 - In the constructor it is allowed to enter zero addresses, this is a problem, since if that happened most of the functionalities would be disabled.

- L690/691 - The getStakerPosition() function has two inputs and inside they are used in the opposite order, this makes the order counterintuitive, the correct thing would be AssetType _assetType, address _staker.

- L2 - Pragma float This contracts in scope are floating the pragma version.

- L875/897/927/995/1010/1044/1057/1124/1137 - In the function _stakeS1Citizen()/_stakeS2Citizen()/_stakeBytes()/_stakeLP() two low level calls are made, this is done in the code without respecting the check effect interact pattern and it is also done at the beginning of the function without being necessary and can be done almost at the end of the function.

- L1274 - The way in which this if is implemented: "if (pool.totalPoints != 0)" is quite uncomfortable to visualize, the correct thing to have less indentation is this:
if (pool.totalPoints == 0) return (0, 0); and then continue with the code.


**BYTES2.sol**
- L2 - Pragma float This contracts in scope are floating the pragma version.

- L101/102 - In the upgradeBytes() function a IByteContract burn is made, but the check effect interact pattern is not respected. It would be advisable to follow this pattern.

- L75 - In the constructor it is allowed to enter zero addresses, this is a problem, since if that happened most of the functionalities would be disabled.

- L114 - The documentation says that only the S1 Citizen only calls it, but this function is external
and it doesn't have any modifier, therefore anyone can call it, this is a contradiction between the function and its documentation.
