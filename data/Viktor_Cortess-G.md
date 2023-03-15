### [G] SETTING THE CONSTRUCTOR TO PAYABLE
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of msg.value == 0 and saves 13 gas on deployment with no security risks.


### [G] USE CONSTANTS INSTEAD OF TYPE(UINTX).MAX
type(uint120).max or type(uint112).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

**contracts\staking\NeoTokyoStaker.sol**

    965: uint256 timelockMultiplier = _timelock & type(uint128).max;

### [G] USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

**contracts\staking\NeoTokyoStaker.sol**

    1212: -  if (uint8(_assetType) > 4) {
           + if (uint256(_assetType) > 4) {
Gas before:
NeoTokyoStaker  ·  stake                            ·      157609  ·     447178  ·     300688  ·           60  ·   
Deployment: 4572791
Gas after:
NeoTokyoStaker  ·  stake                            ·      157603  ·     447172  ·     300682  ·           60  ·
Deployment: 4572155

    1720: if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {

### [G] USE ASSEMBLY TO WRITE ADDRESS STORAGE VALUES

**contracts\staking\BYTES2.sol**

    constructor (
		address _bytes,
		address _s1Citizen,
		address _staker,
		address _treasury
	) {
    -	BYTES1 = _bytes;
    -	S1_CITIZEN = _s1Citizen;
    -	STAKER = _staker;
    -	TREASURY = _treasury;
	}
    + assembly {
            sstore(BYTES1.slot, _ bytes)
        }
    + assembly {
            sstore(CITIZEN.slot, _ s1Citizen)
        }
    + assembly {
            sstore(STAKER.slot, _ staker)
        }
    + assembly {
            sstore(TREASURY.slot, _ treasury)
        }
Similar operations can be done in the following parts:
    `166: STAKER = _staker;`
    `177: TREASURY = _treasury;`

**contracts\staking\NeoTokyoStaker.sol**

    598-603: BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
		LP = _lpToken;
		IDENTITY = _identity;
		VAULT = _vault;
    1766: LP = _lp;

### [G] OPTIMIZE FUNCTIONS' NAMES
Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas is added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.

For example, in the Stake contract user can claim his rewards as often as he wants, so functions `claimReward()` and `getPoolReward()` should be the cheapest for calling.

Now: `d279c191`  =>  `claimReward(address)`
Can be changed to `claimReward_66(address)` => `00e3f7f3`
Now: `e1e6c3ff`  =>  `getPoolReward(AssetType,address)`
Can be changed to `getPoolReward_293(AssetType,address)` => `009a34ce`
