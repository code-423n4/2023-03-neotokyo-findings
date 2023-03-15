# [L-01] Precision Loss for Frequent [BYTES2.getReward()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L114) Callers

Calling `BYTES2.getReward()` frequently may cause precision loss causing some stakers to earn more/less than others even when staking the same assets with all other factors equal.

## Precision Loss 1: Calculating Reward Logic

BYTES2.getReward() calls NetTokyoStaker.claimReward() @ [BYTES.sol#L120](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L120)

NeoTokyoStaker.claimReward() calls [getPoolReward()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1264) for each type of staked asset @ 
- [assetType: S1_CITIZEN > NeoTokyoStaker.sol#L1419](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1419)
- [assetType: S2_CITIZEN > NeoTokyoStaker.sol#L1423](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1423)
- [assetType: LP > NeoTokyoStaker.sol#L1427](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1427)

[NeoTokyoStaker.getPoolReward()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1264) has a division before subtraction precision loss @ lines [NeoTokyoStaker.sol#L1390-L1392](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1390-L1392)

```solidity
share /= _PRECISION;
daoShare /= _PRECISION;
return ((share - daoShare), daoShare);
```

which can potentially be changed to:
```solidity
return ((share - daoShare) / _PRECISION, daoShare / _PRECISION);
```

Thus, more frequent a user calls to [BYTES2.getReward()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L114) may compound the loss.


## Precision Loss 2: Staking Logic

Possible precision loss with `_stake()` functions @ [NeoTokyoStaker.sol#L1229-L1254](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1229-L1254)

Please see `Test / POCs` section (below) for details.

## Tests / POCs

Below is a modified hardhat test script (`NeoTokyoStaker_precisionLoss.test.js`), run with `npx hardhat test`.

I wanted to run the tests with the same block timestamps and as the same user to minimize variable changes between runs. I `console.log()` key values to the console for manual verification. The interesting output here is the difference between lines `alice balance after 360 days` and `alice balance after 360 days (claiming daily)` for each respective test.

The output should look something like this.

```text
  Testing BYTES 2.0 & Neo Tokyo Staker - precision loss
    with example configuration

      with staked S1 Citizens - 30, 90, 180
alice balance before:  BigNumber { value: "4562003739937691084305" }
alice balance after 360 days:  BigNumber { value: "67979932735486377046680" }
        ✓ S1 Citizens give correct reward - claimed yearly
alice balance before:  BigNumber { value: "4562003739937691084305" }
alice balance after 360 days (claiming daily):  BigNumber { value: "67979932735486377046552" }
        ✓ S1 Citizens give correct reward - claimed daily

      with staked S1 Citizens - 360,360,360
alice balance before:  BigNumber { value: "4562003568535052908449" }
alice balance after 360 days:  BigNumber { value: "65672001603835957323655" }
        ✓ S1 Citizens give correct reward - claimed yearly
alice balance before:  BigNumber { value: "4562003568535052908449" }
alice balance after 360 days (claiming daily):  BigNumber { value: "65672001603835957323655" }
        ✓ S1 Citizens give correct reward - claimed daily
```

I have summarized the results in this table

Test Config | balance after claiming once @ 360 days | balance after claiming daily for 360 days | difference
--- | --- | --- | ---
with staked S1 Citizens (staking days = bob: 30 \| alice: 90, 180) | 67979932735486377046680 | 67979932735486377046552 | 128
with staked S1 Citizens (staking days = bob: 360 \| alice: 360, 360) | 65672001603835957323655 | 65672001603835957323655 | 0

An interesting note is that assets that are staked for different periods of time are not subject to the same precision loss. 

I originally though the precision loss in issue [L-01] [NeoTokyoStaker.sol#L1388-L1392](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1388-L1392) can account for this (below)?

```
uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
share /= _PRECISION;
daoShare /= _PRECISION;
return ((share - daoShare), daoShare);
```

However, running tests after patching the code (below) still results in precision loss.
```
uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
return ((share - daoShare) / _PRECISION, daoShare / _PRECISION);
```

Thus, I just wanted to mention other possible places for precision loss.

In test above I only tested for S1 Citizens precision loss, this leads me to believe there may be additional precision loss in the the stake functions that use the `_timelock` parameter, as this parameter was changed between the hardhat tests:

- with staked S1 Citizens (staking days = bob: 30 | alice: 90, 180)
- with staked S1 Citizens (staking days = bob: 360 | alice: 360, 360)

The [NeoTokyoStaker._stakeS1Citizen()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L875) function and is tied to the `_timelock` parameter which flows into setting `citizenStatus.points`. The `citizenStatus.points` variable is read @ [NeoTokyoStaker.sol#L1283](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1283) which is used to calculate rewards @ [NeoTokyoStaker.sol#L1388](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1388). The most probable place for precision loss here are between [NeoTokyoStaker._stakeS1Citizen #L962-L978](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L962-L978) where `citizenStatus.points` and `citizenStatus.points` are both set. While I have not tested for S2 citizens, I believe they may suffer from the same precision loss in the function [NeoTokyoStaker._stakeS2Citizen()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L995) around lines [L1016-L1030](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1016-L1030). 

I'm still new and learning so I'm not 100% confident there is a precision loss in all these places, but I'm wondering if there is a way to rework the algorithms to become a bit more accurate? I unfortunately do not have the time to dig deeper, but perhaps this small degree of loss is acceptable. I'm unsure what is considered normal/acceptable in the crypto space, based on the loss being so minimal compared to the full rewards given, I am submitting this as a Low.

Test Hardhat Script: `NeoTokyoStaker_precisionLoss.test.js`

```javascript
'use strict';

// Imports.
import { ethers } from 'hardhat';
import { expect, should } from 'chai';
import {
	CLASS_TO_ID, ASSETS, DEDUCE_IDENTITY_CREDIT_YIELDS, IDENTITY_CREDIT_POINTS, 
	VAULT_MULTIPLIERS, VAULT_CREDIT_MULTIPLIER_IDS, TIMELOCK_OPTION_IDS, 
	S1_DAO_SHARE, S2_DAO_SHARE, LP_DAO_SHARE
} from './utils/constants';
import {
	prepareContracts
} from './utils/setup';
should();

/*
	Test the updated BYTES token and the staker for correct behavior. Describe the
	contract testing suite, retrieve testing wallets, and create contract
	factories from the artifacts we are testing.
*/
describe('Testing BYTES 2.0 & Neo Tokyo Staker - precision loss', function () {

	// Track the current time and snapshot ID for reverting between tests.
	let currentTime, snapshotId;

	// Prepare several testing callers.
	let owner, alice, bob, nonCitizen, whale, treasury;

	// Neo Tokyo S1 contract instances.
	let beckLoot, NTItems, NTLandDeploy, vaultBox, NTCitizenDeploy, NTOldBytes;

	// Neo Tokyo S2 contract instances.
	let NTOuterCitizenDeploy, NTOuterIdentity, NTS2Items, NTS2LandDeploy;

	// Various mock testing contracts.
	let CitizenMint, VaultMint, IdentityMint, LPToken;

	// New Neo Tokyo BYTES 2.0 and staker contract instances.
	let NTBytes2_0, NTStaking;

	// Prepare the Neo Tokyo ecosystem before each test.
	before(async () => {
		const signers = await ethers.getSigners();
		const addresses = await Promise.all(
			signers.map(async (signer) => signer.getAddress())
		);

		// Prepare testing callers.
		owner = {
			provider: signers[0].provider,
			signer: signers[0],
			address: addresses[0]
		};
		alice = {
			provider: signers[1].provider,
			signer: signers[1],
			address: addresses[1]
		};
		bob = {
			provider: signers[2].provider,
			signer: signers[2],
			address: addresses[2]
		};
		nonCitizen = {
			provider: signers[3].provider,
			signer: signers[3],
			address: addresses[3]
		};
		whale = {
			provider: signers[4].provider,
			signer: signers[4],
			address: addresses[4]
		};
		treasury = {
			provider: signers[5].provider,
			signer: signers[5],
			address: addresses[5]
		};

		// Prepare all of the Neo Tokyo S1, S2, and new contracts for testing.
		[
			beckLoot, NTItems, NTLandDeploy, vaultBox, NTCitizenDeploy, NTOldBytes, 
			VaultMint, IdentityMint, LPToken, CitizenMint, NTOuterCitizenDeploy, 
			NTOuterIdentity, NTS2Items, NTS2LandDeploy, NTBytes2_0, NTStaking
		] = await prepareContracts(treasury.address);
	});
	
	// Accelerate tests by taking snapshot of the current block before each test.
	beforeEach(async function () {
		currentTime = (await ethers.provider.getBlock()).timestamp;
		snapshotId = await network.provider.send('evm_snapshot');
	});
	
	// Revert to the snapshot block after each test.
	afterEach(async function () {
		await network.provider.send('evm_revert', [ snapshotId ]);
	});

	// Prepare to test staker functionality.
	describe('with example configuration', function () {

		// Track IDs for Bob's S1 Citizen components.
		let identityId, vaultId, itemCacheId, landDeedId;

		// The ID of Bob's S1 Citizen.
		let citizenId1;

		// Track IDs for Alice's no-Vault S1 Citizen components.
		let identityIdNoVault, vaultIdNoVault,itemCacheIdNoVault,
			landDeedIdNoVault; 

		// The ID of Alice's no-Vault S1 Citizen.
		let citizenNoVault;

		// Track IDs for Alice's S1 Hand Citizen components.
		let identityIdHand, vaultIdHand, itemCacheIdHand, landDeedIdHand; 

		// The ID of Alice's S1 Hand Citizen.
		let citizenHandOfCitadel;

		// The IDs of Bob's S2 Citizens.
		let s2One, s2Two;

		// Mint tokens to the testing callers before each test.
		before(async () => {

			// Claim testing Identities with Bob and Alice.
			identityId = ethers.BigNumber.from('1');
			identityIdNoVault = ethers.BigNumber.from('2');
			identityIdHand = ethers.BigNumber.from('3');
			await beckLoot.connect(bob.signer).claim(identityId);
			await beckLoot.connect(alice.signer).claim(identityIdNoVault);
			await beckLoot.connect(alice.signer).claim(identityIdHand);

			// Claim testing Vaults with Bob and Alice.
			vaultId = ethers.BigNumber.from('1');
			vaultIdNoVault = ethers.BigNumber.from('2');
			vaultIdHand = ethers.BigNumber.from('3');
			await vaultBox.connect(bob.signer).claim(vaultId);
			await vaultBox.connect(alice.signer).claim(vaultIdNoVault);
			await vaultBox.connect(alice.signer).claim(vaultIdHand);	

			// Claim S1 Items with Bob and Alice, starting from the 2501 default.
			await NTItems.connect(bob.signer).buyItems();
			await NTItems.connect(alice.signer).buyItems();
			await NTItems.connect(alice.signer).buyItems();
			itemCacheId = ethers.BigNumber.from('2501');
			itemCacheIdNoVault = ethers.BigNumber.from('2502');
			itemCacheIdHand = ethers.BigNumber.from('2503');
			expect(
				await NTItems.ownerOf(itemCacheId)
			).to.be.equal(bob.address);
			expect(
				await NTItems.ownerOf(itemCacheIdNoVault)
			).to.be.equal(alice.address);
			expect(
				await NTItems.ownerOf(itemCacheIdHand)
			).to.be.equal(alice.address);

			// Claim S1 Land with Bob and Alice, starting from the 2501 default.
			await NTLandDeploy.connect(bob.signer).buyLand();
			await NTLandDeploy.connect(alice.signer).buyLand();
			await NTLandDeploy.connect(alice.signer).buyLand();
			landDeedId = ethers.BigNumber.from('2501');
			landDeedIdNoVault = ethers.BigNumber.from('2502');
			landDeedIdHand = ethers.BigNumber.from('2503');			

			// Approve Bob's tokens before transfer to the S1 assembler.
			await beckLoot
				.connect(bob.signer)
				.approve(NTCitizenDeploy.address, identityId);
			await vaultBox
				.connect(bob.signer)
				.approve(NTCitizenDeploy.address, vaultId);
			await NTItems.connect(bob.signer).approve(
				NTCitizenDeploy.address,
				itemCacheId
			);
			await NTLandDeploy.connect(bob.signer).approve(
				NTCitizenDeploy.address,
				landDeedId
			);

			// Approve Alice's tokens before transfer to the S1 assembler.
			await beckLoot
				.connect(alice.signer)
				.approve(NTCitizenDeploy.address, identityIdNoVault);
			await beckLoot
				.connect(alice.signer)
				.approve(NTCitizenDeploy.address, identityIdHand);
			await vaultBox
				.connect(alice.signer)
				.approve(NTCitizenDeploy.address, vaultIdHand);
			await NTItems.connect(alice.signer).approve(
				NTCitizenDeploy.address,
				itemCacheIdNoVault
			);
			await NTItems.connect(alice.signer).approve(
				NTCitizenDeploy.address,
				itemCacheIdHand
			);
			await NTLandDeploy.connect(alice.signer).approve(
				NTCitizenDeploy.address,
				landDeedIdNoVault
			);
			await NTLandDeploy.connect(alice.signer).approve(
				NTCitizenDeploy.address,
				landDeedIdHand
			);

			// Specify testing credit multipliers on the S1 Vaults.
			await VaultMint.setCreditMultiplier(
				vaultId,
				VAULT_CREDIT_MULTIPLIER_IDS.High
			);
			await VaultMint.setCreditMultiplier(
				vaultIdNoVault,
				VAULT_CREDIT_MULTIPLIER_IDS['Very High']
			);
			await VaultMint.setCreditMultiplier(
				vaultIdHand,
				VAULT_CREDIT_MULTIPLIER_IDS['Very High']
			);

			/*
				Specify reward rates on the mock data contract for the S1 Citizens that 
				are about to exist.
			*/
			await CitizenMint.setRewardRates(
				identityId,
				ethers.BigNumber.from('5')
			);
			await CitizenMint.setRewardRates(
				identityIdNoVault,
				ethers.BigNumber.from('3')
			);
			await CitizenMint.setRewardRates(
				identityIdHand,
				ethers.BigNumber.from('13')
			);

			/*
				The specified S1 Citizen reward rates and Vault credit multipliers mean 
				that:
				- Bob's S1 Citizen has an Identity "Credit Yield" of "Low"
				- Alice's Vaultless Citizen has one of "Mid"
				- Alice's Hand Citizen has one of "High"
				This method of deducing Identity "Credit Yield" is required because the
				"Credit Yield" trait is not otherwise accessible to a smart contract.
			*/

			// Assemble Bob's S1 Citizen.
			await NTCitizenDeploy.connect(bob.signer).createCitizen(
				identityId,
				vaultId,
				itemCacheId,
				landDeedId,
				false,
				''
			);
			citizenId1 = await NTCitizenDeploy.totalSupply();
			expect(
				await NTCitizenDeploy.ownerOf(citizenId1)
			).to.equal(bob.address);

			// Assemble Alice's S1 Citizen with no Vault.
			await NTCitizenDeploy.connect(alice.signer).createCitizen(
				identityIdNoVault,
				0,
				itemCacheIdNoVault,
				landDeedIdNoVault,
				false,
				''
			);
			citizenNoVault = await NTCitizenDeploy.totalSupply();
			expect(
				await NTCitizenDeploy.ownerOf(citizenNoVault)
			).to.equal(alice.address);

			// Assemble Alice's S1 Hand Citizen.
			await NTCitizenDeploy.connect(alice.signer).createCitizen(
				identityIdHand,
				vaultIdHand,
				itemCacheIdHand,
				landDeedIdHand,
				false,
				''
			);
			citizenHandOfCitadel = await NTCitizenDeploy.totalSupply();
			expect(
				await NTCitizenDeploy.ownerOf(citizenHandOfCitadel)
			).to.equal(alice.address);

			// Set the classes of Bob and Alice's new S1 Citizens.
			await IdentityMint.setClass(
				await NTCitizenDeploy.getIdentityIdOfTokenId(citizenId1),
				CLASS_TO_ID['Assistant']
			);
			await IdentityMint.setClass(
				await NTCitizenDeploy.getIdentityIdOfTokenId(citizenNoVault), 
				CLASS_TO_ID['Samurai']
			);
			await IdentityMint.setClass(
				await NTCitizenDeploy.getIdentityIdOfTokenId(citizenHandOfCitadel), 
				CLASS_TO_ID['Hand of Citadel']
			);

			// Have Bob approve the staker to transfer his S1 Citizen.
			await NTCitizenDeploy.connect(bob.signer).approve(
				NTStaking.address,
				citizenId1
			);

			// Have Alice approve the staker to transfer her S1 Citizens.
			await NTCitizenDeploy.connect(alice.signer).approve(
				NTStaking.address,
				citizenNoVault
			);
			await NTCitizenDeploy.connect(alice.signer).approve(
				NTStaking.address,
				citizenHandOfCitadel
			);

			// Have Alice approve the staker to transfer her unattached Vault.
			await vaultBox.connect(alice.signer).approve(
				NTStaking.address,
				vaultIdNoVault
			);

			/**
				A helper function to create an S2 Citizen for the specific holder.

				@param _citizenHolder The holder (testing user) to create an S2 Citizen 
					for.
			*/
			async function createS2Citizen (_citizenHolder) {
				let identityIdS2, itemCacheIdS2, landDeedIdS2;
				identityIdS2 = (
					await NTOuterIdentity.totalSupply()
				).add(ethers.BigNumber.from('1'));

				/*
					Use administrative powers to claim a new S2 Identity for the desired 
					`_citizenHolder`.
				*/
				await NTOuterIdentity.connect(owner.signer).ownerClaim(identityIdS2);
				await NTOuterIdentity.connect(owner.signer).transferFrom(
					owner.address,
					_citizenHolder.address,
					identityIdS2
				);

				// Claim a new S2 Item for the desired holder.
				itemCacheIdS2 = (
					await NTS2Items.totalSupply()
				).add(ethers.BigNumber.from('1'));
				await NTS2Items.connect(owner.signer).emergencyClaim(
					identityIdS2,
					itemCacheIdS2
				);
				await NTS2Items.connect(owner.signer).transferFrom(
					owner.address,
					_citizenHolder.address,
					itemCacheIdS2
				);

				// Claim a new S2 Land Deed for the desired holder.
				landDeedIdS2 = (
					await NTS2LandDeploy.totalSupply()
				).add(ethers.BigNumber.from('1'));
				await NTS2LandDeploy.connect(owner.signer).emergencyClaim(
					identityIdS2,
					landDeedIdS2
				);
				await NTS2LandDeploy.connect(owner.signer).transferFrom(
					owner.address,
					_citizenHolder.address,
					landDeedIdS2
				);
	
				// Approve the transfer of the holder's components.
				await NTOuterIdentity.connect(_citizenHolder.signer).approve(
					NTOuterCitizenDeploy.address,
					identityIdS2
				);
				await NTS2Items.connect(_citizenHolder.signer).approve(
					NTOuterCitizenDeploy.address,
					itemCacheIdS2
				);
				await NTS2LandDeploy.connect(_citizenHolder.signer).approve(
					NTOuterCitizenDeploy.address,
					landDeedIdS2
				);
	
				// Assemble the holder's S2 Citizen.
				await NTOuterCitizenDeploy.connect(_citizenHolder.signer).createCitizen(
					identityIdS2,
					itemCacheIdS2,
					landDeedIdS2,
					false,
					''
				);

				// Return the ID of the new S2 Citizen.
				return NTOuterCitizenDeploy.totalSupply();
			}

			// Create two S2 Citizens for Bob.
			s2One = await createS2Citizen(bob);
			s2Two = await createS2Citizen(bob);

			// Confirm Bob owns two S2 Citizens.
			expect(
				await NTOuterCitizenDeploy.balanceOf(bob.address)
			).to.be.be.equal(2);

			// Have Bob approve the staker to transfer his S2 Citizens.
			await NTOuterCitizenDeploy.connect(bob.signer).approve(
				NTStaking.address,
				s2One
			);
			await NTOuterCitizenDeploy.connect(bob.signer).approve(
				NTStaking.address,
				s2Two
			);

			// Simulate the migration to BYTES 2.0 by jumping into the future.
			ethers.provider.send('evm_increaseTime', [ 1000000000 ]);
			ethers.provider.send('evm_mine');

			// Have Bob claim his S1 Citizen's BYTES reward.
			await NTCitizenDeploy.connect(bob.signer).getReward();
			let bobBalance = await NTOldBytes.balanceOf(bob.address);

			// Update Neo Tokyo contracts with the address of BYTES 2.0.
			await NTLandDeploy.setBytesAddress(NTBytes2_0.address);
			await NTCitizenDeploy.setBytesAddress(NTBytes2_0.address);
			await NTOuterCitizenDeploy.setBytesAddress(NTBytes2_0.address);
			await NTS2Items.setBytesAddress(NTBytes2_0.address);
			await NTS2LandDeploy.setBytesAddress(NTBytes2_0.address);

			// Perform the BYTES to BYTES 2.0 migration with Bob's tokens.
			await NTBytes2_0.connect(bob.signer).upgradeBytes(bobBalance);

			// Transfer half of Bob's new BYTES to Alice for staking testing.
			let half = bobBalance.div(2);
			await NTBytes2_0.connect(bob.signer).approve(
				alice.address,
				ethers.constants.MaxUint256  
			);
			await NTBytes2_0.connect(alice.signer).transferFrom(
				bob.address,
				alice.address,
				half
			);
			await NTBytes2_0.connect(bob.signer).approve(
				NTStaking.address,
				ethers.constants.MaxUint256  
			);
			await NTBytes2_0.connect(alice.signer).approve(
				NTStaking.address,
				ethers.constants.MaxUint256  
			);

			// Mint testing LP tokens to users and approve transfer to the staker.
			await LPToken.mint(alice.address, ethers.utils.parseEther('100'));
			await LPToken.mint(bob.address, ethers.utils.parseEther('100'));
			await LPToken.mint(whale.address, ethers.utils.parseEther('10000'));
			await LPToken.connect(alice.signer).approve(
				NTStaking.address,
				ethers.constants.MaxUint256
			);
			await LPToken.connect(bob.signer).approve(
				NTStaking.address,
				ethers.constants.MaxUint256
			);
			await LPToken.connect(whale.signer).approve(
				NTStaking.address,
				ethers.constants.MaxUint256
			);

			// Configure each of the stakeable assets with the timelock options.
			await NTStaking.connect(owner.signer).configureTimelockOptions(
				ASSETS.S1_CITIZEN.id,
				[ ...Array(ASSETS.S1_CITIZEN.timelockOptions.length).keys() ],
				ASSETS.S1_CITIZEN.timelockOptions
			);
			await NTStaking.connect(owner.signer).configureTimelockOptions(
				ASSETS.S2_CITIZEN.id,
				[ ...Array(ASSETS.S2_CITIZEN.timelockOptions.length).keys() ],
				ASSETS.S2_CITIZEN.timelockOptions
			);
			await NTStaking.connect(owner.signer).configureTimelockOptions(
				ASSETS.BYTES.id,
				[ ...Array(ASSETS.BYTES.timelockOptions.length).keys() ],
				ASSETS.BYTES.timelockOptions
			);
			await NTStaking.connect(owner.signer).configureTimelockOptions(
				ASSETS.LP.id,
				[ ...Array(ASSETS.LP.timelockOptions.length).keys() ],
				ASSETS.LP.timelockOptions
			);

			// Configure each of the stakeable assets with daily reward rates.
			await NTStaking.connect(owner.signer).configurePools(
				[
					{
						assetType: ASSETS.S1_CITIZEN.id,
						daoTax: S1_DAO_SHARE,
						rewardWindows: [
							{
								startTime: 0,
								reward: ethers.utils.parseEther('200').div(60 * 60 * 24)
							}
						]
					},
					{
						assetType: ASSETS.S2_CITIZEN.id,
						daoTax: S2_DAO_SHARE,
						rewardWindows: [
							{
								startTime: 0,
								reward: ethers.utils.parseEther('100').div(60 * 60 * 24)
							}
						]
					},
					{
						assetType: ASSETS.LP.id,
						daoTax: LP_DAO_SHARE,
						rewardWindows: [
							{
								startTime: 0,
								reward: ethers.utils.parseEther('50').div(60 * 60 * 24)
							}
						]
					}
				]
			);

			// Configure the staker with its S1 Identity "Credit Yield" data.
			await NTStaking.connect(owner.signer).configureIdentityCreditYields(
				DEDUCE_IDENTITY_CREDIT_YIELDS.citizenRewardRates,
				DEDUCE_IDENTITY_CREDIT_YIELDS.vaultMultipliers,
				DEDUCE_IDENTITY_CREDIT_YIELDS.identityCreditYieldRates
			);

			// Configure the staker with its S1 Identity base point values.
			await NTStaking.connect(owner.signer).configureIdentityCreditPoints(
				IDENTITY_CREDIT_POINTS.identityCreditYieldRates,
				IDENTITY_CREDIT_POINTS.points
			);

			// Configure the staker with its S1 Vault credit multiplier values.
			await NTStaking.connect(owner.signer).configureVaultCreditMultipliers(
				VAULT_MULTIPLIERS.vaultMultipliers,
				VAULT_MULTIPLIERS.configuredMultipliers
			);
		});


		// Simulate S1 Citizen staking.
		describe('with staked S1 Citizens - 30, 90, 180', function () {
			// number of days to simulate
			const SIMULATE_DAYS = 360
			
			let bobStakeTime, aliceStakeTime;
			beforeEach(async () => {

				// Bob stakes his S1 Citizen for 30 days.
				await NTStaking.connect(bob.signer).stake(
					ASSETS.S1_CITIZEN.id,
					TIMELOCK_OPTION_IDS['30'],
					citizenId1,
					0,
					0
				);

				// Get the time at which Bob staked.
				let priorBlockNumber = await ethers.provider.getBlockNumber();
				let priorBlock = await ethers.provider.getBlock(priorBlockNumber);
				bobStakeTime = priorBlock.timestamp;

				// Alice stakes her S1 Citizen with an additional Vault for 90 days.
				await NTStaking.connect(alice.signer).stake(
					ASSETS.S1_CITIZEN.id,
					TIMELOCK_OPTION_IDS['90'],
					citizenNoVault,
					vaultIdNoVault,
					0
				);

				// Alice stakes her S1 Hand Citizen for 180 days.
				await NTStaking.connect(alice.signer).stake(
					ASSETS.S1_CITIZEN.id,
					TIMELOCK_OPTION_IDS['180'],
					citizenHandOfCitadel,
					0,
					1
				);


				// Get the time at which Alice staked.
				priorBlockNumber = await ethers.provider.getBlockNumber();
				priorBlock = await ethers.provider.getBlock(priorBlockNumber);
				aliceStakeTime = priorBlock.timestamp;
			});

			// Test Alice and Bob's stake with a reward rate-change mid-stake.
			it('S1 Citizens give correct reward - claimed yearly', async function () {
				
				/** CUSTOM TESTING **/

				await NTBytes2_0.connect(bob.signer).getReward(alice.address);
				let aliceBalanceBefore = await NTBytes2_0.balanceOf(alice.address)
				console.log("alice balance before: ", aliceBalanceBefore)


				// // Simulate Alice staking for SIMULATE_DAYS days.
				const _1DAY = (60 * 60 * 24)
				await ethers.provider.send('evm_setNextBlockTimestamp', [
					aliceStakeTime + _1DAY * SIMULATE_DAYS
				]);

				await NTBytes2_0.connect(bob.signer).getReward(alice.address);     // bob claims alice's reward
				let aliceBalanceAfterXDays = await NTBytes2_0.balanceOf(alice.address)
				console.log(`alice balance after ${SIMULATE_DAYS} days: `, aliceBalanceAfterXDays)

			});

			// Test Alice and Bob's stake with a reward rate-change mid-stake.
			it('S1 Citizens give correct reward - claimed daily', async function () {

				/** CUSTOM TESTING **/

				await NTBytes2_0.connect(bob.signer).getReward(alice.address);
				let aliceBalanceBefore = await NTBytes2_0.balanceOf(alice.address)
				console.log("alice balance before: ", aliceBalanceBefore)


				// // Simulate Alice staking for SIMULATE_DAYS days.
				const _1DAY = (60 * 60 * 24)

				// await NTCitizenDeploy.connect(alice.signer).getReward();
				for (let i = 1; i <= SIMULATE_DAYS; i++) {
					await ethers.provider.send('evm_setNextBlockTimestamp', [
						aliceStakeTime + _1DAY * i
					]);
					await NTBytes2_0.connect(bob.signer).getReward(alice.address);     // bob claims alice's reward	
				}
			
				let aliceBalanceClaimedDaily = await NTBytes2_0.balanceOf(alice.address)
				console.log(`alice balance after ${SIMULATE_DAYS} days (claiming daily): `, aliceBalanceClaimedDaily)
			});
		});


		// Simulate S1 Citizen staking.
		describe('with staked S1 Citizens - 360,360,360', function () {
			// number of days to simulate
			const SIMULATE_DAYS = 360
			
			let bobStakeTime, aliceStakeTime;
			beforeEach(async () => {

				// Bob stakes his S1 Citizen for 30 days.
				await NTStaking.connect(bob.signer).stake(
					ASSETS.S1_CITIZEN.id,
					TIMELOCK_OPTION_IDS['360'],
					citizenId1,
					0,
					0
				);

				// Get the time at which Bob staked.
				let priorBlockNumber = await ethers.provider.getBlockNumber();
				let priorBlock = await ethers.provider.getBlock(priorBlockNumber);
				bobStakeTime = priorBlock.timestamp;

				// Alice stakes her S1 Citizen with an additional Vault for 90 days.
				await NTStaking.connect(alice.signer).stake(
					ASSETS.S1_CITIZEN.id,
					TIMELOCK_OPTION_IDS['360'],
					citizenNoVault,
					vaultIdNoVault,
					0
				);

				// Alice stakes her S1 Hand Citizen for 180 days.
				await NTStaking.connect(alice.signer).stake(
					ASSETS.S1_CITIZEN.id,
					TIMELOCK_OPTION_IDS['360'],
					citizenHandOfCitadel,
					0,
					1
				);


				// Get the time at which Alice staked.
				priorBlockNumber = await ethers.provider.getBlockNumber();
				priorBlock = await ethers.provider.getBlock(priorBlockNumber);
				aliceStakeTime = priorBlock.timestamp;
			});

			// Test Alice and Bob's stake with a reward rate-change mid-stake.
			it('S1 Citizens give correct reward - claimed yearly', async function () {
				
				/** CUSTOM TESTING **/

				await NTBytes2_0.connect(bob.signer).getReward(alice.address);
				let aliceBalanceBefore = await NTBytes2_0.balanceOf(alice.address)
				console.log("alice balance before: ", aliceBalanceBefore)


				// // Simulate Alice staking for SIMULATE_DAYS days.
				const _1DAY = (60 * 60 * 24)
				await ethers.provider.send('evm_setNextBlockTimestamp', [
					aliceStakeTime + _1DAY * SIMULATE_DAYS
				]);

				await NTBytes2_0.connect(bob.signer).getReward(alice.address);     // bob claims alice's reward
				let aliceBalanceAfterXDays = await NTBytes2_0.balanceOf(alice.address)
				console.log(`alice balance after ${SIMULATE_DAYS} days: `, aliceBalanceAfterXDays)

			});

			// Test Alice and Bob's stake with a reward rate-change mid-stake.
			it('S1 Citizens give correct reward - claimed daily', async function () {

				/** CUSTOM TESTING **/

				await NTBytes2_0.connect(bob.signer).getReward(alice.address);
				let aliceBalanceBefore = await NTBytes2_0.balanceOf(alice.address)
				console.log("alice balance before: ", aliceBalanceBefore)


				// // Simulate Alice staking for SIMULATE_DAYS days.
				const _1DAY = (60 * 60 * 24)

				// await NTCitizenDeploy.connect(alice.signer).getReward();
				for (let i = 1; i <= SIMULATE_DAYS; i++) {
					await ethers.provider.send('evm_setNextBlockTimestamp', [
						aliceStakeTime + _1DAY * i
					]);
					await NTBytes2_0.connect(bob.signer).getReward(alice.address);     // bob claims alice's reward	
				}
			
				let aliceBalanceClaimedDaily = await NTBytes2_0.balanceOf(alice.address)
				console.log(`alice balance after ${SIMULATE_DAYS} days (claiming daily): `, aliceBalanceClaimedDaily)
			});
		});
	});
});
```


# [L-02] BYTES2.getReward() is `external` and can be called by anyone to `getReward()` of another user
 
[BYTES2.getReward()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L114) is an `external` function that does not validate `msg.sender`. A malicious user could frequently call `getReward()` on a victim which may result in the victim having less reward than they would normally receive based on precision loss from [L-01](#L-01). This would be very expensive for the attacker and the amount of rewards the victim will miss out on is very minimal. While this scenario may be unlikely to cause significant harm today, it may be prudent to implement access controls around [BYTES2.getReward()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L114) to prevent any future changes from exacerbating this behavior.


# [L-03] Incorrect conditional @ [NeoTokyoStaker.sol#L1668](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668)

```solidity
1668:  if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
```

Based on the switch() statement @ [NeoTokyoStaker.sol#L1683-L1694](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1683-L1694) the conditional should be should replace the `> 4` with a `> 3`: 

```solidity
1668:  if (uint8(_assetType) == 2 || uint8(_assetType) > 3) {
```

Even if an `_assetType` with a value of `4` is configured, [_withdraw()](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1697) will fail and revert as the `_withdraw` variable is never set. The user will risk losing more gas from failing to revert early due to bad input. I do not see much other risk to the contract due to this.