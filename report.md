---
sponsor: "Neo Tokyo"
slug: "2023-03-neotokyo"
date: "2023-04-11"
title: "Neo Tokyo contest"
findings: "https://github.com/code-423n4/2023-03-neotokyo-findings/issues"
contest: 220
---

# Overview

## About C4

Code4rena (C4) is an open organization consisting of security researchers, auditors, developers, and individuals with domain expertise in smart contracts.

A C4 audit contest is an event in which community participants, referred to as Wardens, review, audit, or analyze smart contract logic in exchange for a bounty provided by sponsoring projects.

During the audit contest outlined in this document, C4 conducted an analysis of the Neo Tokyo smart contract system written in Solidity. The audit contest took place between March 8—March 15 2023.

## Wardens

136 Wardens contributed reports to the Neo Tokyo contest:

  1. 0x1337
  2. 0x1f8b
  3. [0x52](https://twitter.com/IAm0x52)
  4. [0x6980](https://twitter.com/0x6980)
  5. [0xAgro](https://twitter.com/0xAgro)
  6. [0xSmartContract](https://twitter.com/0xSmartContract)
  7. [0xSolus](https://twitter.com/0xSolus)
  8. 0xWeiss
  9. 0xhacksmithh
  10. 0xkazim
  11. [0xnev](https://twitter.com/bigbuttdev)
  12. [ABA](https://twitter.com/abarbatei)
  13. [Angry\_Mustache\_Man](https://twitter.com/AdityaK0010)
  14. [Aymen0909](https://github.com/Aymen1001)
  15. BPZ (pa6221, Bitcoinfever244, and PrasadLak)
  16. Bjorn\_bug
  17. [BowTiedOriole](https://twitter.com/BowTiedOriole)
  18. ChainReview
  19. [DadeKuma](https://twitter.com/DadeKuma)
  20. DeFiHackLabs (SunSec, gbaleee, 0x4non, [Aits](https://twitter.com/tamjid0x01), and [Rappie](https://twitter.com/rappenstein2))
  21. Deathstore
  22. [DevABDee](https://twitter.com/DevABDee)
  23. Diana
  24. [Dravee](https://twitter.com/BowTiedDravee)
  25. Dug
  26. Englave
  27. Flora
  28. Go-Langer
  29. Haipls
  30. IceBear
  31. [Inspex](https://twitter.com/InspexCo) (Resistor, jokopoppo, DeStinE21, mimic_f, Rugsurely, and ErbaZZ)
  32. J4de
  33. [JCN](https://twitter.com/0xJCN)
  34. [Jeiwan](https://jeiwan.net)
  35. Josiah
  36. Kek
  37. Krace
  38. Kresh
  39. LegendFenGuin
  40. Lirios
  41. [MadWookie](https://twitter.com/wookiemad)
  42. Madalad
  43. [MatricksDeCoder](https://twitter.com/Zed_Blockchain)
  44. MiniGlome
  45. MyFDsYours
  46. R-Nemes
  47. RaymondFam
  48. ReyAdmirado
  49. Rolezn
  50. [Ruhum](https://twitter.com/0xruhum)
  51. SAAJ
  52. [Sathish9098](https://www.linkedin.com/in/sathishkumar-p-26069915a)
  53. [Shubham](https://twitter.com/Shaping_Myself)
  54. [Taloner](https://twitter.com/DevTaloner)
  55. [Toshii](https://twitter.com/0xToshii)
  56. UdarTeam (ahmedov and tourist)
  57. [Udsen](https://github.com/udsene)
  58. Viktor\_Cortess
  59. [aashar](https://twitter.com/0xaash)
  60. [adriro](https://twitter.com/adrianromero)
  61. ak1
  62. anodaram
  63. arialblack14
  64. ast3ros
  65. atharvasama
  66. [auditor0517](https://twitter.com/auditor0517)
  67. ayden
  68. brgltd
  69. btk
  70. [c3phas](https://twitter.com/c3ph_)
  71. [carlitox477](https://twitter.com/carlitox477)
  72. [catellatech](https://twitter.com/CatellaTech)
  73. cccz
  74. chaduke
  75. [codeislight](https://twitter.com/codeIslight)
  76. [ctrlc03](https://twitter.com/ctrl_c3)
  77. [deadrxsezzz](https://twitter.com/deadrxsezzz)
  78. descharre
  79. [dharma09](https://twitter.com/im_Dharma09)
  80. [durianSausage](https://github.com/lyciumlee)
  81. erictee
  82. [fatherOfBlocks](https://twitter.com/father0fBl0cks)
  83. [favelanky](https://twitter.com/favelanky)
  84. ginlee
  85. glcanvas
  86. [handsomegiraffe](https://twitter.com/chewonithard1)
  87. [hunter\_w3b](https://twitter.com/hunt3r_w3b)
  88. jasonxiale
  89. jekapi
  90. [joestakey](https://twitter.com/JoeStakey)
  91. [juancito](https://twitter.com/0xJuancito)
  92. [kaden](https://twitter.com/0xKaden)
  93. [kenzo](https://twitter.com/KenzoAgada)
  94. kutugu
  95. [lemonr](https://twitter.com/ronexondimu)
  96. [leopoldjoy](https://twitter.com/leopoldjoy)
  97. luxartvinsec
  98. [martin](https://github.com/martin-petrov03)
  99. matrix\_0wl
  100. [minhquanym](https://www.linkedin.com/in/minhquanym/)
  101. [mrpathfindr](https://twitter.com/0xpathfindr)
  102. [nadin](https://twitter.com/nadin20678790)
  103. [navinavu](https://twitter.com/zeroxnavinavu)
  104. [nobody2018](https://twitter.com/nobody20185)
  105. [oyc\_109](https://twitter.com/andyfeili)
  106. parsely
  107. peanuts
  108. [pfedprog](https://twitter.com/pfedprog)
  109. pipoca
  110. rbserver
  111. rokso
  112. saian
  113. santipu\_
  114. schrodinger
  115. scokaf (Scoon and jauvany)
  116. [sinarette](twitter.com/noots0)
  117. [slvDev](https://twitter.com/slvDev)
  118. [tsvetanovv](https://twitter.com/cvetanovv0)
  119. ubl4nk
  120. ulqiorra
  121. volodya
  122. [yamapyblack](https://twitter.com/yamapyblack)
  123. [zaskoh](https://twitter.com/0xzaskoh)

This contest was judged by [hansfriese](https://twitter.com/hansfriese).

Final report assembled by [liveactionllama](https://twitter.com/liveactionllama).

# Summary

The C4 analysis yielded an aggregated total of 2 unique vulnerabilities. Of these vulnerabilities, 2 received a risk rating in the category of HIGH severity and 0 received a risk rating in the category of MEDIUM severity.

Additionally, C4 analysis included 83 reports detailing issues with a risk rating of LOW severity or non-critical. There were also 46 reports recommending gas optimizations.

All of the issues presented here are linked back to their original finding.

# Scope

The code under review can be found within the [C4 Neo Tokyo contest repository](https://github.com/code-423n4/2023-03-neotokyo), and is composed of 2 smart contracts written in the Solidity programming language and includes 969 lines of Solidity code.

# Severity Criteria

C4 assesses the severity of disclosed vulnerabilities based on three primary risk categories: high, medium, and low/non-critical.

High-level considerations for vulnerabilities span the following key areas when conducting assessments:

- Malicious Input Handling
- Escalation of privileges
- Arithmetic
- Gas use

For more information regarding the severity criteria referenced throughout the submission review process, please refer to the documentation provided on [the C4 website](https://code4rena.com), specifically our section on [Severity Categorization](https://docs.code4rena.com/awarding/judging-criteria/severity-categorization).

# High Risk Findings (2)
## [[H-01] Updating a pool's total points doesn't affect existing stake positions for rewards calculation](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/423)
*Submitted by [adriro](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/423), also found by [kutugu](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/462), [joestakey](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/417), [Madalad](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/416), [rbserver](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/380), [minhquanym](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/350), [minhquanym](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/347), [auditor0517](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/321), [sinarette](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/277), [ast3ros](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/255), [ABA](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/185), [Haipls](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/121), [J4de](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/104), and [Dug](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/19)*

Staking rewards are calculated based on the user's share of total points in the corresponding asset pool, this is the sum of the points associated to the staker's positions divided by the total points from all positions in the pool. We can see this calculation in the `getPoolReward` function:

[NeoTokyoStaker.sol#L1386-L1393](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1386-L1393)

```solidity
// Return final shares.
unchecked {
    uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
    uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
    share /= _PRECISION;
    daoShare /= _PRECISION;
    return ((share - daoShare), daoShare);
}
```

However, note that `pool.totalPoints` is the current value of the pool's total point at the time the function `getPoolReward` is called. It isn't related to the time the user staked their position, or isn't affected in any way by other stake/unstake actions from potentially other users.

This means that any action that modifies the pool's total points (stake or unstake) won't affect current staking positions, as previously opened staking positions won't accrue their rewards correctly. For stake actions, it will cause rewards from existing staking positions to be reduced, as their calculation of the shares now divided by a higher `pool.totalPoints` value. From unstake actions, it will cause rewards from existing staking positions to be incorrectly increased, as the calculation of the shares is now divided by a lower `pool.totalPoints` value. See section "Proof of Concept" for a more detailed walkthrough.

In a similar way, this could also be used by a griefer to intentionally harm another user. As the `getReward` function present in the `BYTES2` contract is permissionless (anyone can call this on behalf of an arbitrary account), a bad actor can call this when the pool's total points is high, which will have the effect of reducing the user rewards.

### Proof of Concept

Let's assume the pool is empty. Alice stakes at `t1` an asset worth 100 points and Bob stakes at `t2` another asset worth 100 points. In order to simplify the examples, let's also consider that all periods fall in the same window, thus having a constant reward rate.

**Alice claims after Bob stakes**

In this scenario, Alice claims her rewards in `t3` after Bob stakes. She will get less rewards from the `[t1, t2]` period, as the calculation will consider the entire period `[t1, t3]` and calculate the shares using 200 points. Here the correct way would be to calculate the period `[t1, t2]` using 100 total points, and the period `[t2, t3]` using 100 total points.

1.  Alice stakes at `t1` and gets 100 points. Total points is 100.
2.  Bob stakes at `t2` and gets 100 points. Total points is 200.
3.  Alice claims rewards at `t3`. She will get less rewards since the calculation will be done using 200 points.

**Alice and Bob stake at same time**

Here, `t1 == t2` and Bob and Alice stake at the same time. Alice unstakes at `t3` and Bob claims rewards at `t4`. In this case, Bob will get more rewards, as the calculation will consider the entire period `[t1, t4]` and calculate the shares using 100 points. Here the correct way would be to calculate the period `[t1, t3]` using 200 total points, and the period `[t3, t4]` using 100 total points.

1.  Alice and Bob stake at `t1 == t2` and each one gets 100 points. Total points is 200.
2.  Alice unstakes at `t3`. Total points is 100.
3.  Bob claims rewards at `t4`. He will get more rewards since the calculation will be done using 100 points.

**Griefer intentionally claims rewards of Alice**

As described in the previous section, a bad actor can intentionally claim the rewards of another user at a time the pool has a high value for total points, since this call as this is a permissionless action.

1.  Alice stakes at `t1` and gets 100 points. Total points is 100.
2.  Bob stakes at `t2` and gets 100 points. Total points is 200.
3.  Bad actor claims rewards of Alice at `t3`. She will get less rewards since the calculation will be done using 200 points.

### Recommendation

Rewards calculation should track reward rate according to modifications in the pool's total points caused by stake or unstake actions.

My recommendation for a performant solution would be to follow [this staking example](https://solidity-by-example.org/defi/staking-rewards/) or [the full Staking contract from Synthetix](https://github.com/Synthetixio/synthetix/blob/develop/contracts/StakingRewards.sol). The principal idea here is that every action that affects rewards triggers the `updateReward` modifier, which updates the `rewardPerTokenStored` variable that tracks the reward amount per staked token. A similar idea could be adapted to track the reward per point for the current contract. Stake and unstake actions should update this variable before modifying the pool's total points.

**[TimTinkers (Neo Tokyo) confirmed and commented](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/423#issuecomment-1477147709):**
 > Excellent catch, good write-up.



***

## [[H-02] Underflow of `lpPosition.points` during withdrawLP causes huge reward minting](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/261)
*Submitted by [rokso](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/261), also found by [0xnev](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/463), [BPZ](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/450), [Toshii](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/434), [Josiah](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/424), [ak1](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/408), [ulqiorra](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/400), [rbserver](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/378), [juancito](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/374), [juancito](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/366), [minhquanym](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/348), [auditor0517](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/323), [auditor0517](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/322), [Jeiwan](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/305), [cccz](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/303), [RaymondFam](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/267), [rokso](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/263), [MadWookie](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/254), [kaden](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/243), [0x52](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/232), [carlitox477](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/230), [DadeKuma](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/210), [Krace](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/209), [Haipls](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/204), [Krace](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/194), [UdarTeam](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/187), [kenzo](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/172), [DadeKuma](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/170), [ABA](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/165), [jekapi](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/147), [J4de](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/140), [anodaram](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/122), [LegendFenGuin](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/100), [aashar](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/67), [nobody2018](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/60), [Ruhum](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/28), and [Lirios](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/16)*

NeoTokyoStaking allows to stake and withdraw LPs. User can stake multiple times on same position which simply results in extended lock time and user can withdraw all of these LPs once lock time is passed.

There is a scenario when withdrawing LPs results in overflow of [lpPosition.points](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1627). After withdraw if attacker calls `getRewards()` then attacker will get more than 1e64 BYTES tokens as reward.

### Proof of Concept

Affected code block: [NeoTokyoStaker.sol#L1622-L1631](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1622-L1631)

Affected line: [L1627](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1627)

From below POC, you can see that Alice is staking twice and some specific amounts which will trigger underflow when Alice withdraw LP. Once staked LPs are unlocked, Alice can withdraw her LPs and call `getReward()` to trigger minting of more than 1e64 BYTES tokens.

Below test can be added in `NeoTokyoStaker.test.js` test file.

```js
		it('Unexpected rewards minting due to underflow of "points"', async function () {
			// Configure the LP token contract address on the staker.
			await NTStaking.connect(owner.signer).configureLP(LPToken.address);
			const amount1 = ethers.utils.parseEther('10.009')
			const amount2 = ethers.utils.parseEther('11.009')
			const lockingDays = 30
			
			// Alice stake amount1 LPs for 30 days.
			await NTStaking.connect(alice.signer).stake(
				ASSETS.LP.id,
				TIMELOCK_OPTION_IDS[lockingDays],
				amount1,
				0,
				0
			);

			// Alice stake amount2 LPs for 30 days.
			await NTStaking.connect(alice.signer).stake(
				ASSETS.LP.id,
				TIMELOCK_OPTION_IDS[lockingDays],
				amount2,
				0,
				0
			);

			const priorBlockNumber = await ethers.provider.getBlockNumber();
			const priorBlock = await ethers.provider.getBlock(priorBlockNumber);
			let aliceStakeTime = priorBlock.timestamp;
			
			// Bob stake 10 LPs for 30 days
			await NTStaking.connect(bob.signer).stake(
				ASSETS.LP.id,
				TIMELOCK_OPTION_IDS[lockingDays],
				ethers.utils.parseEther('10'),
				0,
				0
			);

			// Set time to unlock staked lp
			await ethers.provider.send('evm_setNextBlockTimestamp', [
				aliceStakeTime + (60 * 60 * 24 * lockingDays)
			]);
			
			// Alice withdraw LP
                        // This transaction will cause underflow of `lpPosition.points`
			await NTStaking.connect(alice.signer).withdraw(
				ASSETS.LP.id,
				amount1.add(amount2)
			);

			// Before exploit:: Verify Alice's Bytes balance is less than 10000 BYTES
			expect(await NTBytes2_0.balanceOf(alice.address)).lt(ethers.utils.parseUnits('10000', 18))
			
			// Get rewards for Alice. It will mint HUGE rewards due to underflow happened on withdraw transaction.
			await NTBytes2_0.getReward(alice.address)

			// After exploit:: Verify Alice's Bytes balance is greater than 3e64
			expect(await NTBytes2_0.balanceOf(alice.address)).gt(ethers.utils.parseUnits('3', 64))
		});
```

### Recommended Mitigation Steps

Consider adding proper precision for `points` and `totalPoints` and also consider checking for under/overflows.

**[TimTinkers (Neo Tokyo) commented](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/261#issuecomment-1477324787):**
 > @hansfriese - this attack is a different way of abusing the same rounding bug from [#348](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/348); duplicates?
> 
> I agree with the severity of the underlying issue and really appreciate the test case demonstrating this.

**[hansfriese (judge) commented](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/261#issuecomment-1477516448):**
 > Totally, there are 3 kinds of rounding issues.
> 1. Users can get infinite points by depositing `5e15` twice and withdrawing `1e16`. So `0 * 2 - 1 = -1 = type(uint256).max`
> 2. Users can get free points by depositing 1e16 and withdrawing 5e15 twice. So `1 - 0 * 2 = 1`
> 3. Users would lose some LP(or staking reward) due to the rounding.
> 
> After discussing with other judges, I will merge 1 and 2 into one high and mark 3 as QA as it contains a lower impact.

**[TimTinkers (Neo Tokyo) confirmed](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/261#issuecomment-1482544958)**



***

# Low Risk and Non-Critical Issues

For this contest, 76 reports were submitted by wardens detailing low risk and non-critical issues. The [report highlighted below](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/83) by **0xSmartContract** received the top score from the judge.

*The following wardens also submitted reports: [joestakey](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/456), [Udsen](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/455), [Kresh](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/447), [atharvasama](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/443), [Deathstore](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/441), [favelanky](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/426), [0x1f8b](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/419), [ulqiorra](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/407), [carlitox477](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/404), [zaskoh](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/401), [rbserver](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/397), [saian](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/395), [0xkazim](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/390), [BPZ](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/376), [btk](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/370), [Go-Langer](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/365), [nadin](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/361), [slvDev](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/357), [tsvetanovv](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/354), [rokso](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/353), [minhquanym](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/351), [Diana](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/346), [peanuts](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/345), [0x6980](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/342), [Viktor\_Cortess](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/327), [MyFDsYours](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/319), [codeislight](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/316), [Jeiwan](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/309), [martin](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/301), [erictee](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/297), [SAAJ](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/295), [DadeKuma](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/291), [BowTiedOriole](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/285), [glcanvas](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/278), [handsomegiraffe](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/258), [0xSolus](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/249), [Kek](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/240), [IceBear](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/239), [ayden](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/238), [luxartvinsec](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/231), [brgltd](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/226), [Haipls](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/218), [DevABDee](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/217), [Dravee](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/216), [0xAgro](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/214), [catellatech](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/200), [descharre](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/199), [DeFiHackLabs](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/190), [Inspex](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/175), [RaymondFam](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/169), [ABA](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/166), [Madalad](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/153), [jekapi](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/150), [ChainReview](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/146), [santipu\_](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/144), [yamapyblack](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/139), [MatricksDeCoder](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/138), [oyc\_109](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/126), [matrix\_0wl](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/110), [scokaf](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/106), [Englave](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/102), [jasonxiale](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/89), [ubl4nk](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/81), [Dug](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/77), [pfedprog](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/73), [chaduke](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/70), [Sathish9098](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/65), [fatherOfBlocks](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/54), [mrpathfindr](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/48), [parsely](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/42), [Rolezn](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/40), [lemonr](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/31), [deadrxsezzz](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/14), [Taloner](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/11), and [0xhacksmithh](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/4).*

## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]|Low-level calls that are unnecessary for the system should be avoided | 2 |
|[L-02]|Insufficient coverage |  |
|[L-03]|Project Upgrade and Stop Scenario should be |  |
|[L-04]|Division before multiplication causing significant loss of precision |1 |
|[L-05]|Update codes to avoid Compile Errors| 3 |
|[L-06]|`Claim` event is missing parameters| 1 |
|[L-07]|Project has NPM Dependency which uses a vulnerable version : `@openzeppelin`| 1 |
|[L-08]|Keccak Constant values should used to immutable rather than constant | 7 |
|[L-09]|In the `constructor` , there is no return of incorrect address identification | 1 |

Total 10 issues

### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01] |Omissions in Events|1|
| [N-02] |`Function writing` that does not comply with the `Solidity Style Guide` ||
| [N-03] |Tokens accidentally sent to the contract cannot be recovered| All Contracts |
| [N-04] |Floating pragma| 2 |
| [N-05] |Use SMTChecker| |
| [N-06] |Constants on the left are better| 4 |
| [N-07] |`constants` should be defined rather than using magic numbers| 3 |
| [N-08] |Use the `delete` keyword instead of assigning a value of `0`|8 |
| [N-09] |Use a single file for all system-wide constants |12 |
| [N-10] |According to the syntax rules, use ` => mapping (` instead of ` => mapping(` using spaces as keyword|2 |
| [N-11] |For modern and more readable code; update import usages| 9 |
| [N-12] |Assembly Codes Specific – Should Have Comments | 7 |
| [N-13] |Take advantage of Custom Error's return value property| 3 |
| [N-14] |Pragma version^0.8.19  version too recent to be trusted| 1 |
| [N-15] |Inconsistent Solidity Versions | 21 |

Total 16 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |You can explain the operation of critical functions in NatSpec with an infographic |

Total 1 suggestion

## [L-01] Low-level calls that are unnecessary for the system should be avoided 

Low-level calls that are unnecessary for the system should be avoided whenever possible because low-level calls behave differently from a contract-type call. For example;

`address.call(abi.encodeWithSelector("fancy(bytes32)", mybytes))`` does not verify that a target is actually a contract, while
ContractInterface(address).fancy(mybytes)` does. 

Additionally, when calling out to functions declared view/pure, the solidity compiler would actually perform a staticcall providing additional security guarantees while a low-level call does not. Similarly, return values have to be decoded manually when performing low-level calls.

Note: if a low-level call needs to be performed, consider relying on Contract.function.selector instead of encoding using a hardcoded ABI string.

```solidity

2 results

contracts/staking/NeoTokyoStaker.sol:
  772: 		(bool success, bytes memory data) = 
  773: 			_asset.call(
  774: 				abi.encodeWithSelector(
  775: 					_TRANSFER_FROM_SELECTOR,
  776: 					_from,
  777: 					_to, 
  778: 					_idOrAmount
  779: 				)
  780: 			);
  781: 
  782: 		// Revert if the low-level call fails.
  783: 		if (!success) {
  784: 			revert(string(data));
  785: 		}




  801: 		(bool success, bytes memory data) = 
  802: 			_asset.call(
  803: 				abi.encodeWithSelector(
  804: 					_TRANSFER_SELECTOR,
  805: 					_to, 
  806: 					_amount
  807: 				)
  808: 			);
  809: 
  810: 		// Revert if the low-level call fails.
  811: 		if (!success) {
  812: 			revert(string(data));
  813: 		}
  814: 	}


```

### Recommendation
When calling out to a contract known in the system, always prefer typed contract calls (interfaces/contract) instead of low-level calls.

This is to avoid errors, potentially unchecked return values, have security guarantees.

## [L-02] Insufficient coverage

### Description
The test coverage rate of the project is ~85%. Testing all functions is best practice in terms of security criteria.

Due to its capacity, test coverage is expected to be 100%.


```js
---------------------------|----------|----------|----------|----------|----------------|
File                       |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
---------------------------|----------|----------|----------|----------|----------------|
 staking/                  |       95 |    74.53 |    88.57 |    89.84 |                |
  BYTES2.sol               |    78.95 |    83.33 |       75 |    71.43 |... 151,152,154 |
  NeoTokyoStaker.sol       |    96.09 |       74 |    92.59 |    90.96 |... 5,1855,1856 |
---------------------------|----------|----------|----------|----------|----------------|

```

## [L-03] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

## [L-04] Division before multiplication causing significant loss of precision

First divides and then multiplies again, there is a significant loss of precision;

```solidity

contracts/staking/NeoTokyoStaker.sol:
  1385  
  1386: 			// Return final shares.
  1387: 			unchecked {
  1388: 				uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
  1389: 				uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
  1390: 				share /= _PRECISION;
  1391: 				daoShare /= _PRECISION;
  1392: 				return ((share - daoShare), daoShare);
  1393: 			}
  1394  		}
```

### Recommended Mitigation Steps
Multiply first before dividing to keep the precision.

## [L-05] Update codes to avoid Compile Errors

```js
Warning: Function state mutability can be restricted to pure
   --> contracts/staking/BYTES2.sol:245:2:
    |
245 |   function updateReward (
    |   ^ (Relevant source part starts here and spans across multiple lines).


Warning: Function state mutability can be restricted to pure
   --> contracts/staking/BYTES2.sol:261:2:
    |
261 |   function updateRewardOnMint (
    |   ^ (Relevant source part starts here and spans across multiple lines).

Warning: Contract code size is 63151 bytes and exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on Mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
   --> contracts/staking/NeoTokyoStaker.sol:190:1:
    |
190 | contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
    | ^ (Relevant source part starts here and spans across multiple lines).

```

## [L-06] `Claim` event is missing parameters

The `NeoTokyoStaker.sol` contract has very important function; `Claim` 

However, only amounts are published in emits, whereas msg.sender must be specified in every transaction, a contract or web page listening to events cannot react to users, emit does not serve the purpose.

Basically, this event cannot be used


```diff
contracts/staking/NeoTokyoStaker.sol:
  1444  
  1445: 		// Emit an event.
  1446: 		emit Claim (
+  			msg.sender
  1447: 			_recipient,
  1448: 			totalReward,
  1449: 			totalTax
  1450: 		);


```

### Recommended Mitigation Steps
Add `msg.sender` parameter in event-emit

## [L-07] Project has NPM Dependency which uses a vulnerable version : `@openzeppelin`

```js
1 result - 1 file

package.json:
  1: {
  9:     "@openzeppelin/contracts-upgradeable": "^4.4.2",
 
```

```js
VULNERABILITY	VULNERABLE VERSION
M
Incorrect Calculation	
>=4.8.0 <4.8.2
M
Incorrect Calculation	
>=4.8.0 <4.8.2
H
Improper Verification of Cryptographic Signature	
<4.7.3
M
Denial of Service (DoS)	
>=3.2.0 <4.7.2
L
Incorrect Resource Transfer Between Spheres	
>=4.6.0 <4.7.2
H
Incorrect Calculation	
>=4.3.0 <4.7.2
H
Information Exposure	
>=4.1.0 <4.7.1
H
Information Exposure	
>=4.0.0 <4.7.1
```

### Proof Of Concept
https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts-upgradeable

### Recommended Mitigation Steps
Upgrade OZ to version 4.8.0 or higher

## [L-08] Keccak Constant values should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.

```solidity
7 results - 2 files

contracts/staking/BYTES2.sol:
  36  	/// The identifier for the right to perform token burns.
  37: 	bytes32 public constant BURN = keccak256("BURN");
  38  
  39  	/// The identifier for the right to perform some contract changes.
  40: 	bytes32 public constant ADMIN = keccak256("ADMIN");
  41  

contracts/staking/NeoTokyoStaker.sol:
  205  	/// The identifier for the right to configure the LP token address.
  206: 	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
  207  
  208  	/// The identifier for the right to configure timelock options.
  209: 	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
  
  213  	/// The identifier for the right to configure Identity and Vault points.
  214: 	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
  215  
  216  	/// The identifier for the right to configure emission rates and the DAO tax.
  217: 	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
  218  
  219  	/// The identifier for the right to configure BYTES staking caps.
  220: 	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");

```

## [L-09] In the `constructor`, there is no return of incorrect address identification

In case of incorrect address definition in the `constructor` , there is no way to fix it because of the variables are immutable.

It is recommended to fix the architecture:<br>
1. Address definitions can be done changeable architecture
2. Because of `owner = address(0)` at the end of the constructor, there is no way to fix it, so the owner's authority can be maintained.

```solidity
contracts/staking/BYTES2.sol:
  74  	*/
  75: 	constructor (
  76: 		address _bytes,
  77: 		address _s1Citizen,
  78: 		address _staker,
  79: 		address _treasury
  80: 	) {
  81: 		BYTES1 = _bytes;
  82: 		S1_CITIZEN = _s1Citizen;
  83: 		STAKER = _staker;
  84: 		TREASURY = _treasury;
  85: 	}

```

## [N-01] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters.

```diff

contracts/staking/NeoTokyoStaker.sol:
  1043  	*/
  1044: 	function _stakeBytes (
  1045: 		uint256
  1046: 	) private {
  1047: 		uint256 amount;
  1048: 		uint256 citizenId;
  1049: 		uint256 seasonId;
  1050: 		assembly{
  1051: 			amount := calldataload(0x44)
  1052: 			citizenId := calldataload(0x64)
  1053: 			seasonId := calldataload(0x84)
  1054: 		}
  1055: 
  1056: 		// Attempt to transfer BYTES to escrow.
  1057: 		_assetTransferFrom(BYTES, msg.sender, address(this), amount);
  1058: 
  1059: 		// Handle staking BYTES into an S1 Citizen.
  1060: 		if (seasonId == 1) {
  1061: 			StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
  1062: 			uint256 cap = VAULT_CAP;
  1063: 			if (!citizenStatus.hasVault) {
  1064: 				cap = NO_VAULT_CAP;
  1065: 			}
  1066: 			if (citizenStatus.stakedBytes + amount > cap) {
  1067: 				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
  1068: 			}
  1069: 
  1070: 			// Validate that the caller actually staked the Citizen.
  1071: 			if (citizenStatus.timelockEndTime == 0) {
  1072: 				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
  1073: 			}
  1074: 
  1075: 			PoolData storage pool = _pools[AssetType.S1_CITIZEN];
  1076: 			unchecked {
  1077: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
  1078: 				citizenStatus.stakedBytes += amount;
  1079: 				citizenStatus.points += bonusPoints;
  1080: 				pool.totalPoints += bonusPoints;
  1081: 			}
  1082: 
  1083: 		// Handle staking BYTES into an S2 Citizen.
  1084: 		} else if (seasonId == 2) {
  1085: 			StakedS2Citizen storage citizenStatus = stakedS2[msg.sender][citizenId];
  1086: 			uint256 cap = NO_VAULT_CAP;
  1087: 			if (citizenStatus.stakedBytes + amount > cap) {
  1088: 				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
  1089: 			}
  1090: 
  1091: 			// Validate that the caller actually staked the Citizen.
  1092: 			if (citizenStatus.timelockEndTime == 0) {
  1093: 				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
  1094: 			}
  1095: 
  1096: 			PoolData storage pool = _pools[AssetType.S2_CITIZEN];
  1097: 			unchecked {
  1098: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
  1099: 				citizenStatus.stakedBytes += amount;
  1100: 				citizenStatus.points += bonusPoints;
  1101: 				pool.totalPoints += bonusPoints;
  1102: 			}
  1103: 
  1104: 		// Revert because an invalid season ID has been supplied.
  1105: 		} else {
  1106: 			revert InvalidSeasonId(seasonId);
  1107: 		}
  1108: 
  1109: 		// Emit an event.
  1110: 		emit Stake(
+			citizenId
  1111: 			msg.sender,
  1112: 			BYTES,
  1113: 			(seasonId << 128) + citizenId,
  1114: 			amount
  1115: 		);
  1116: 	}

```

## [N-02] `Function writing` that does not comply with the `Solidity Style Guide`

### Context
All Contracts

### Description
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

## [N-03] Tokens accidentally sent to the contract cannot be recovered

### Context
contracts/staking/NeoTokyoStaker.sol:

It can't be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```

## [N-04] Floating pragma

### Description
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.<br>
https://swcregistry.io/docs/SWC-103

Floating Pragma List: 

```solidity

2 results - 2 files

contracts/staking/BYTES2.sol:
  1  // SPDX-License-Identifier: AGPL-3.0-only
  2: pragma solidity ^0.8.19;
  3  

contracts/staking/NeoTokyoStaker.sol:
  1  // SPDX-License-Identifier: AGPL-3.0-only
  2: pragma solidity ^0.8.19;
  3 
```

### Recommendation
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

## [N-05] Use SMTChecker

The *highest* tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

## [N-06] Constants on the left are better

If you use the constant first you support structures that veil programming errors. And one should only produce code either to add functionality, fix an programming error or trying to support structures to avoid programming errors (like design patterns). 

https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html

```solidity

4 results - 1 file

contracts/staking/NeoTokyoStaker.sol:
   910: 		if (citizenVaultId != 0 && vaultId != 0) {
   917: 		} else if (citizenVaultId != 0 && vaultId == 0) {
   926: 		} else if (citizenVaultId == 0 && vaultId != 0) {
  1834: 				if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
```

## [N-07] `constants` should be defined rather than using magic numbers

A magic number is a numeric literal that is used in the code without any explanation of its meaning. The use of magic numbers makes programs less readable and hence more difficult to maintain and update.

Even assembly can benefit from using readable constants instead of hex/numeric literals.

```solidity

contracts/staking/BYTES2.sol:
  149  		*/
  150: 		uint256 treasuryShare;
  151: 		unchecked {
  152: 			treasuryShare = _amount * 2 / 3; 
  153: 		}
  154: 		_mint(TREASURY, treasuryShare);
  155  	}

contracts/staking/NeoTokyoStaker.sol:
  1076  			unchecked {
  1077: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);

  1097  			unchecked {
  1098: 				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);


```

## [N-08] Use the `delete` keyword instead of assigning a value of `0`

Using the 'delete' keyword instead of assigning a '0' value is a detailed optimization that increases code readability and audit quality, and clearly indicates the intent.

Other hand, if use `delete` instead `0` value assign , it will be gas saved.

```solidity

8 results - 1 file

contracts/staking/NeoTokyoStaker.sol:
  1517: 		stakedCitizen.stakedBytes = 0;
  1518: 		stakedCitizen.timelockEndTime = 0;
  1519: 		stakedCitizen.points = 0;
  1521: 		stakedCitizen.stakedVaultId = 0;
  1582: 		stakedCitizen.stakedBytes = 0;
  1583: 		stakedCitizen.timelockEndTime = 0;
  1584: 		stakedCitizen.points = 0;
  1635: 			lpPosition.multiplier = 0;
```

## [N-09] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol<br>
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity

12 results - 2 files

contracts/staking/BYTES2.sol:
  37: 	bytes32 public constant BURN = keccak256("BURN");
  40: 	bytes32 public constant ADMIN = keccak256("ADMIN");

contracts/staking/NeoTokyoStaker.sol:
  191: 	bytes4 constant private _TRANSFER_FROM_SELECTOR = 0x23b872dd;
  194: 	bytes4 constant private _TRANSFER_SELECTOR = 0xa9059cbb;
  197: 	uint256 constant private _PRECISION = 1e12;
  200: 	uint256 constant private _DIVISOR = 100;
  203: 	uint256 constant private _BYTES_PER_POINT = 200 * 1e18;
  206: 	bytes32 public constant CONFIGURE_LP = keccak256("CONFIGURE_LP");
  209: 	bytes32 public constant CONFIGURE_TIMELOCKS = keccak256(
  214: 	bytes32 public constant CONFIGURE_CREDITS = keccak256("CONFIGURE_CREDITS");
  217: 	bytes32 public constant CONFIGURE_POOLS = keccak256("CONFIGURE_POOLS");
  220: 	bytes32 public constant CONFIGURE_CAPS = keccak256("CONFIGURE_CAPS");
```

## [N-10] According to the syntax rules, use ` => mapping (` instead of ` => mapping(` using spaces as keyword

```js
contracts/staking/NeoTokyoStaker.sol:
  280: 	mapping ( AssetType => mapping ( uint256 => uint256 )) public	timelockOptions;
  319: 	mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
  326: 	mapping ( uint256 => mapping ( string => string )) public identityCreditYield;


contracts/staking/NeoTokyoStaker.sol:
-  372: 	mapping ( address => mapping( uint256 => StakedS1Citizen )) public stakedS1;
+  372: 	mapping ( address => mapping ( uint256 => StakedS1Citizen )) public stakedS1;

- 405: 	mapping ( address => mapping( uint256 => StakedS2Citizen )) public stakedS2;
+ 405: 	mapping ( address => mapping ( uint256 => StakedS2Citizen )) public stakedS2;

```

## [N-11] For modern and more readable code; update import usages

### Context
```solidity

9 results - 2 files

contracts/staking/BYTES2.sol:
  4: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
  5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
  7: import "../access/PermitControl.sol";
  8: import "../interfaces/IByteContract.sol";
  9: import "../interfaces/IStaker.sol";

contracts/staking/NeoTokyoStaker.sol:
  4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
  6: import "../access/PermitControl.sol";
  7: import "../interfaces/IByteContract.sol";
  8: import "../interfaces/IGenericGetter.sol";

```

### Description
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

### Recommendation
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

## [N-12] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

```solidity
7 results - 1 file

contracts/staking/NeoTokyoStaker.sol:
   833: 		assembly {
   834: 			let length := mload(a)
   886: 		assembly {
   887: 			citizenId := calldataload(0x44)
  1001: 		assembly {
  1002: 			citizenId := calldataload(0x44)
  1236: 		assembly {
  1237: 			switch _assetType
  1461: 		assembly {
  1462: 			citizenId := calldataload(0x24)
  1536: 		assembly {
  1537: 			citizenId := calldataload(0x24)
  1682: 		assembly {
  1683: 			switch _assetType

```

## [N-13] Take advantage of Custom Error's return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the `()` sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

For Example;

```solidity

3 results - 1 file

contracts/staking/NeoTokyoStaker.sol:
  1149: 			revert MismatchedTimelock();
  1712: 			revert LockedConfigurationOfLP();
  1835: 			revert RewardWindowTimesMustIncrease();
```

## [N-14] Pragma version^0.8.19 version too recent to be trusted.

https://github.com/ethereum/solidity/blob/develop/Changelog.md<br>
0.8.19 (2023-02-22)<br>
0.8.17 (2022-09-08)<br>
0.8.16 (2022-08-08)<br>
0.8.15 (2022-06-15)<br>
0.8.10 (2021-11-09)


Unexpected bugs can be reported in recent versions;
- Risks related to recent releases
- Risks of complex code generation changes
- Risks of new language features
- Risks of known bugs

Use a non-legacy and more battle-tested version<br>
Use 0.8.10

## [N-15] Inconsistent Solidity Versions 

### Description
Different Solidity compiler versions are used, the following contracts mix versions: 

```js
21 results - 21 files

contracts/access/PermitControl.sol:
  2: pragma solidity ^0.8.19;

contracts/interfaces/IByteContract.sol:
  2: pragma solidity ^0.8.19;

contracts/interfaces/IGenericGetter.sol:
  2: pragma solidity ^0.8.19;

contracts/interfaces/IStaker.sol:
  2: pragma solidity ^0.8.19;

contracts/s1/beckLoot.sol:
  10: pragma solidity ^0.8.0;

contracts/s1/BYTESContract.sol:
  5: pragma solidity ^0.8.0;

contracts/s1/NTCitizenDeploy.sol:
  10: pragma solidity 0.8.11;

contracts/s1/NTItems.sol:
  10: pragma solidity ^0.8.0;

contracts/s1/NTLandDeploy.sol:
  10: pragma solidity ^0.8.0;

contracts/s1/vaultBox.sol:
  10: pragma solidity ^0.8.0;

contracts/s2/NTOuterCitizenDeploy.sol:
  10: pragma solidity 0.8.11;

contracts/s2/NTOuterIdentity.sol:
  10: pragma solidity ^0.8.0;

contracts/s2/NTS2Items.sol:
  10: pragma solidity ^0.8.0;

contracts/s2/NTS2LandDeploy.sol:
  10: pragma solidity ^0.8.0;

contracts/staking/BYTES2.sol:
  2: pragma solidity ^0.8.19;

contracts/staking/NeoTokyoStaker.sol:
  2: pragma solidity ^0.8.19;

```

### Recommendation
Versions must be consistent with each other.

## [S-01] You can explain the operation of critical functions in NatSpec with an infographic.

An example project;<br>
https://etherscan.io/address/0xe87a68de82204bfa63e4d626d4c5194481cf3b59#code#F1#L244

```js
/**

     *** ---------------------------------- ***
     ***                                    ***
     ***     PUBLIC MINTING FUNCTIONS       ***
     ***                                    ***
     *** ---------------------------------- ***
     ***/
    //                       ,-.
    //                       `-'
    //                       /|\
    //                        |                       ,----------.
    //                       / \                      |ERC721Drop|
    //                     Caller                     `----+-----'
    //                       |          purchase()         |
    //                       | ---------------------------->
    //                       |                             |
    //                       |                             |
    //          ___________________________________________________________
    //          ! ALT  /  drop has no tokens left for caller to mint?      !
    //          !_____/      |                             |               !
    //          !            |    revert Mint_SoldOut()    |               !
    //          !            | <----------------------------               !
    //          !~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //          !~[noop]~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //                       |                             |
    //                       |                             |
    //          ___________________________________________________________
    //          ! ALT  /  public sale isn't active?        |               !
    //          !_____/      |                             |               !
    //          !            |    revert Sale_Inactive()   |               !
    //          !            | <----------------------------               !
    //          !~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //          !~[noop]~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //                       |                             |
    //                       |                             |
    //          ___________________________________________________________
    //          ! ALT  /  inadequate funds sent?           |               !
    //          !_____/      |                             |               !
    //          !            | revert Purchase_WrongPrice()|               !
    //          !            | <----------------------------               !
    //          !~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //          !~[noop]~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~!
    //                       |                             |
    //                       |                             |----.
    //                       |                             |    | mint tokens
    //                       |                             |<---'
    //                       |                             |
    //                       |                             |----.
    //                       |                             |    | emit IERC721Drop.Sale()
    //                       |                             |<---'
    //                       |                             |
    //                       | return first minted token ID|
    //                       | <----------------------------
    //                     Caller                     ,----+-----.
    //                       ,-.                      |ERC721Drop|
    //                       `-'                      `----------'
    //                       /|\
    //                        |
    //                       / \
    /**
```



***

# Gas Optimizations

For this contest, 45 reports were submitted by wardens detailing gas optimizations. The [report highlighted below](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/134) by **JCN** received the top score from the judge.

*The following wardens also submitted reports: [Udsen](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/459), [nadin](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/442), [leopoldjoy](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/429), [Aymen0909](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/421), [0x1f8b](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/418), [hunter\_w3b](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/413), [c3phas](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/412), [carlitox477](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/411), [ulqiorra](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/406), [atharvasama](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/398), [Angry\_Mustache\_Man](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/381), [slvDev](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/356), [Diana](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/338), [dharma09](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/330), [Viktor\_Cortess](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/315), [glcanvas](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/276), [Flora](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/262), [0xSolus](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/248), [ayden](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/237), [SAAJ](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/215), [durianSausage](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/212), [ReyAdmirado](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/201), [descharre](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/198), [Inspex](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/176), [RaymondFam](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/171), [Shubham](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/154), [Madalad](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/152), [pipoca](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/143), [MatricksDeCoder](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/130), [oyc\_109](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/125), [schrodinger](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/124), [matrix\_0wl](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/113), [MiniGlome](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/90), [0xSmartContract](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/86), [Sathish9098](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/64), [volodya](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/58), [fatherOfBlocks](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/53), [mrpathfindr](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/47), [Rolezn](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/41), [ginlee](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/23), [R-Nemes](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/18), [arialblack14](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/13), [0xnev](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/10), and [0xhacksmithh](https://github.com/code-423n4/2023-03-neotokyo-findings/issues/3).*

## Summary
A majority of functions that underwent direct changes for optimziations did not have individual tests that showed gas usage and therefore gas savings were explained via opcodes and EVM gas costs (with the exception of the first issue).

The gas savings in the first issue are substantial and therefore `stake()` and `withdraw()` were benchmarked as POCs for the optimizations that were done in storage and in the `claimReward()` & `getPoolReward()` functions.

### Notes
- Some code snippets may be truncated to save space. Code snippets may also be accompanied by `@audit` tags in comments to aid in explaining the issue.
- The `*` next to the gas savings of an issue indicates that some instances are found within loops, meaning the actual gas savings will be greater depending on the number of iterations in the loop.
- The last issue is soley meant to offer clarification for the issue caught by the c4udit tool.

### Gas Optimizations
| Number |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| G-01 | Refactor mapping to save over 44k gas for new users that stake, 10k gas for recurring users that stake, and 10k gas for users that withdraw | - | 54200 |
| G-02 | State variables can be packed to use fewer storage slots | 1 | 2000 |
| G-03 | State variables can be cached instead of re-reading them from storage | 27 | *2700 |
| G-04 | Avoid emitting constants | 5 | 1875 |
| G-05 | Multiple accesses of a mapping/array should use a storage pointer | 25 | *1000 |
| G-06 | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables | 13 |  260 |
| G-07 | Use storage instead of memory for structs/arrays | - | - |

## [G-01] Refactor mapping to save over 44k gas for new users that stake, 10k gas for recurring users that stake, and 10k gas for users that withdraw

The `lastRewardTime` nested mapping is only modified in the `claimReward()` function and all asset types for the user are given the same value: `block.timestamp` (i.e. 3 different storage slots undergo an SSTORE with the **same value**). The `lastRewardTime` mapping is then accessed in the `getPoolReward()` function and the same values are read for each `AssetType` (i.e. 3 different storage slots, which hold the **same value**, undergo an SLOAD). Due to this flow, the nested mapping is unecessary and a regular mapping would suffice in recording a single `lastRewardTime` for the user since the time will always be the same for ALL assets, as per the `claimReward()` function.

Refactoring the `lastRewardTime` mapping will stop a user from incurring two `Gsset (20000 gas)` and two `Gcoldsload (2100 gas)` when they call `stake()` for the first time, and two `Gsreset (2900 gas)` and two `Gcoldsload (2100 gas)` each time they call `withdraw()` and each subsequent time they call `stake()`.

**Note that the flow in which a new user calls `stake()` and then `withdraw()` will result in ~54,200 gas savings between these two function calls + ~10k gas for each subsequent call to `stake()`/`withdraw()`**.

Since the `claimReward()` and `getPoolReward()` functions do not have individual tests that show gas usage, the `stake()` and `withdraw()` functions will be shown below as a POC for this gas optimization (both `stake()` and `withdraw()` result in the invocation of `claimReward()` and `getPoolReward()`).

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L319

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1310

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1433-L1435

### Gas Savings for `stake()`, obtained via protocol's tests: Avg 26202 gas

**Note that the average gas savings do not reflect the actual savings for this function since `stake()` will see the greatest savings when called by a new user. Observe the difference in the `Max` column, which will illustrate the savings a user will see when calling `stake()` for the first time (i.e. avoiding two `Gsset (20000 gas)` and two `Gcoldsload (2100 gas)`: 44537 gas.**

**In addition, the `MIN` column shows the gas savings of the `stake()` function when called by a recurring user (i.e. avoiding two `Gsreset (2900 gas)` and two `Gcoldsload (2100 gas)`): 10337 gas.**
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  154353  |  423836  |  277026  |    71   |
| After  |  144016  |  379299  |  250824  |    71   |

### Gas Savings for `withdraw()`, obtained via protocol's tests: Avg 9883 gas

**Note that the gas savings for this function will be similar to the savings recurring users experience for the `stake()` function, since in a normal flow the user would call `stake()` first before they call `withdraw()`.**
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  102046  |  267114  |  187080  |    18   |
| After  |  93794   |  256750  |  177197  |    18   |

```solidity
File: contracts/staking/NeoTokyoStaker.sol
319: 		mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;

1310: 		uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType]; // @audit: `Gcoldsload (2100 gas)`, called 3 times in `claimReward()` for each AssetType

1433:		lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp; // @audit: 1st `Gsset (20000 gas)` for new users, `Gsreset (2900 gas) for recurring users
1434:		lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp; // @audit: 2nd `Gsset (20000 gas)` for new users, `Gsreset (2900 gas) for recurring users
1435:		lastRewardTime[_recipient][AssetType.LP] = block.timestamp; // @audit: 3rd `Gsset (20000 gas)` for new users, `Gsreset (2900 gas) for recurring users
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..51df065 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -316,7 +316,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        mapping ( AssetType => PoolData ) private _pools;

        /// Track the last time a caller was granted their rewards for each asset.
-       mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
+       mapping ( address => uint256 ) public lastRewardTime;

        /**
                This admin-configurable double-mapping allows us to deduce the Identity
@@ -1307,7 +1307,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                                applicable time period.
                        */
                        uint256 totalReward;
-                       uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
+                       uint256 lastPoolRewardTime = lastRewardTime[_recipient];
                        uint256 windowCount = pool.rewardCount;
                        for (uint256 i; i < windowCount; ) {
                                RewardWindow memory window = pool.rewardWindows[i];
@@ -1430,9 +1430,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                );

                // Record the current time as the beginning time for checking rewards.
-               lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;
-               lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;
-               lastRewardTime[_recipient][AssetType.LP] = block.timestamp;
+               lastRewardTime[_recipient] = block.timestamp;

                // Calculate total reward and tax.
                uint256 totalReward;
```

## [G-02] State variables can be packed to use fewer storage slots

The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to eachother in storage and this will pack the values together into a single 32 byte storage slot (if the values combined are <= 32 bytes). If the variables packed together are retrieved together in functions we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a `Gwarmaccess (100 gas)` versus a `Gcoldsload (2100 gas)`.

Gas savings: `2000`

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L232-L511

### We are able to pack `lpLocked` and `LP` into one storage slot to save 1 SLOT (~2000 gas).
```solidity
File: contracts/staking/NeoTokyoStaker.sol
231:	/// The address of the LP token contract.
232:	address public LP;
...
510:	/// Whether or not setting the LP token contract address is locked.
511:	bool public lpLocked;
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..075cb0c 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -228,6 +228,9 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        /// The address of the assembled Neo Tokyo S2 Citizen contract.
        address immutable public S2_CITIZEN;

+       /// Whether or not setting the LP token contract address is locked.
+       bool public lpLocked;
+
        /// The address of the LP token contract.
        address public LP;

@@ -507,8 +510,6 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                LPPosition stakedLPPosition;
        }

-       /// Whether or not setting the LP token contract address is locked.
-       bool public lpLocked;

        /**
                This struct records an input to the staker's `configurePools` function.
```

## [G-03] State variables can be cached instead of re-reading them from storage

Caching of a state variable replaces each `Gwarmaccess (100 gas)` with a much cheaper stack read.

Total Instances: `27`

Gas savings: `27 * 100 = 2700` 

**Gas savings will actually be greater since storage slot access is occuring within loops in some instances**

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L710-L752

### Cache `_stakerS1Position[_staker].length` and `_stakerS2Position[_staker].length` to save 2 SLOADs 

*More SLOADs would actually be saved since storage slot access is occuring within a loop.*
```solidity
File: staking/NeoTokyoStaker.sol
710:	function getStakerPositions (
711:		address _staker
712:	) external view returns (StakerPosition memory) {
713:
714:		// Compile the S1 Citizen details.
715:		StakedS1CitizenOutput[] memory stakedS1Details =
716:			new StakedS1CitizenOutput[](_stakerS1Position[_staker].length); // @audit: 1st sload
717:		for (uint256 i; i < _stakerS1Position[_staker].length; ) { // @audit: 2nd sload + on every iteration
718:			uint256 citizenId = _stakerS1Position[_staker][i];
719:			StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
720:			stakedS1Details[i] = StakedS1CitizenOutput({
721:				citizenId: citizenId,
722:				stakedBytes: citizenDetails.stakedBytes,
723:				timelockEndTime: citizenDetails.timelockEndTime,
724:				points: citizenDetails.points,
725:				hasVault: citizenDetails.hasVault,
726:				stakedVaultId: citizenDetails.stakedVaultId
727:			});
728:			unchecked { i++; }
729:		}
730:
731:		// Compile the S2 Citizen details.
732:		StakedS2CitizenOutput[] memory stakedS2Details =
733:			new StakedS2CitizenOutput[](_stakerS2Position[_staker].length); // @audit: 1st sload
734:		for (uint256 i; i < _stakerS2Position[_staker].length; ) { // @audit: 2nd sload + on every iteration
735:			uint256 citizenId = _stakerS2Position[_staker][i];
736:			StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
737:			stakedS2Details[i] = StakedS2CitizenOutput({
738:				citizenId: citizenId,
739:				stakedBytes: citizenDetails.stakedBytes,
740:				timelockEndTime: citizenDetails.timelockEndTime,
741:				points: citizenDetails.points
742:			});
743:			unchecked { i++; }
744:		}
745:
746:		// Return the final output position struct.
747:		return StakerPosition({
748:			stakedS1Citizens: stakedS1Details,
749:			stakedS2Citizens: stakedS2Details,
750:			stakedLPPosition: stakerLPPosition[_staker]
751:		});
752:	}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..8d8ccc4 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -712,9 +712,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        ) external view returns (StakerPosition memory) {

                // Compile the S1 Citizen details.
+               uint256 s1Length = _stakerS1Position[_staker].length;
                StakedS1CitizenOutput[] memory stakedS1Details =
-                       new StakedS1CitizenOutput[](_stakerS1Position[_staker].length);
-               for (uint256 i; i < _stakerS1Position[_staker].length; ) {
+                       new StakedS1CitizenOutput[](s1Length);
+               for (uint256 i; i < s1Length; ) {
                        uint256 citizenId = _stakerS1Position[_staker][i];
                        StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
                        stakedS1Details[i] = StakedS1CitizenOutput({
@@ -729,9 +730,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                }

                // Compile the S2 Citizen details.
+               uint256 s2Length = _stakerS2Position[_staker].length;
                StakedS2CitizenOutput[] memory stakedS2Details =
-                       new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
-               for (uint256 i; i < _stakerS2Position[_staker].length; ) {
+                       new StakedS2CitizenOutput[](s2Length);
+               for (uint256 i; i < s2Length; ) {
                        uint256 citizenId = _stakerS2Position[_staker][i];
                        StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
                        stakedS2Details[i] = StakedS2CitizenOutput({
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L967-L978

### Cache the value from the expression given to `citizenStatus.points` and use that stack variable in other expression to save 1 SLOAD. 

**For example: `stackVar = (expression)` => `citizenStatus.points = stackVar` => `pool.totalPoints += stackVar`.**
```solidity
File: contracts/staking/NeoTokyoStaker.sol
967:		unchecked {
968:			citizenStatus.points = // @audit: can cache expression value as stack variable
969:				identityPoints * vaultMultiplier * timelockMultiplier /
970:				_DIVISOR / _DIVISOR;
971:			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
972:
973:			// Record the caller's staked S1 Citizen.
974:			_stakerS1Position[msg.sender].push(citizenId);
975:
976:			// Update the pool point weights for rewards
977:			pool.totalPoints += citizenStatus.points; // @audit: unecessary sload, use cached value from expression
978:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..0469c3f 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -964,17 +964,19 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.S1_CITIZEN];
+               uint256 points;
                unchecked {
-                       citizenStatus.points =
+                       points =
                                identityPoints * vaultMultiplier * timelockMultiplier /
                                _DIVISOR / _DIVISOR;
+                       citizenStatus.points = points;
                        citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

                        // Record the caller's staked S1 Citizen.
                        _stakerS1Position[msg.sender].push(citizenId);

                        // Update the pool point weights for rewards
-                       pool.totalPoints += citizenStatus.points;
+                       pool.totalPoints += points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1021-L1030

### Optimization is identical to the one above. Saves 1 SLOAD.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1021:		unchecked {
1022:			citizenStatus.points = 100 * timelockMultiplier / _DIVISOR; // @audit: can cache expression value as stack variable
1023:			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
1024:
1025:			// Record the caller's staked S2 Citizen.
1026:			_stakerS2Position[msg.sender].push(citizenId);
1027:
1028:			// Update the pool point weights for rewards
1029:			pool.totalPoints += citizenStatus.points; // @audit: unecessary sload, use cached value from expression
1030:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..7f03c33 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1018,15 +1018,17 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.S2_CITIZEN];
+               uint256 points;
                unchecked {
-                       citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
+                       points = 100 * timelockMultiplier / _DIVISOR;
+                       citizenStatus.points = points;
                        citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

                        // Record the caller's staked S2 Citizen.
                        _stakerS2Position[msg.sender].push(citizenId);

                        // Update the pool point weights for rewards
-                       pool.totalPoints += citizenStatus.points;
+                       pool.totalPoints += points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1044-L1102

### Cache `citizenStatus.stakedBytes` to save 4 SLOADs.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1060:		if (seasonId == 1) {
1061:			StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
1062:			uint256 cap = VAULT_CAP;
1063:			if (!citizenStatus.hasVault) {
1064:				cap = NO_VAULT_CAP;
1065:			}
1066:			if (citizenStatus.stakedBytes + amount > cap) { // @audit: 1st sload
1067:				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap); // @aduit: 2nd sload
1068:			}
1069:
1070:			// Validate that the caller actually staked the Citizen.
1071:			if (citizenStatus.timelockEndTime == 0) {
1072:				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
1073:			}
1074:
1075:			PoolData storage pool = _pools[AssetType.S1_CITIZEN];
1076:			unchecked {
1077:				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
1078:				citizenStatus.stakedBytes += amount; // @audit: 3rd sload
1079:				citizenStatus.points += bonusPoints;
1080:				pool.totalPoints += bonusPoints;
1081:			}
1082:
1083:		// Handle staking BYTES into an S2 Citizen.
1084:		} else if (seasonId == 2) {
1085:			StakedS2Citizen storage citizenStatus = stakedS2[msg.sender][citizenId];
1086:			uint256 cap = NO_VAULT_CAP;
1087:			if (citizenStatus.stakedBytes + amount > cap) { // @audit: 1st sload
1088:				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap); // @audit: 2nd sload
1089:			}
1090:
1091:			// Validate that the caller actually staked the Citizen.
1092:			if (citizenStatus.timelockEndTime == 0) {
1093:				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
1094:			}
1095:
1096:			PoolData storage pool = _pools[AssetType.S2_CITIZEN];
1097:			unchecked {
1098:				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
1099:				citizenStatus.stakedBytes += amount; // @audit: 3rd sload
1100:				citizenStatus.points += bonusPoints;
1101:				pool.totalPoints += bonusPoints;
1102:			}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..93a190d 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1059,12 +1059,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Handle staking BYTES into an S1 Citizen.
                if (seasonId == 1) {
                        StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
+                       uint256 s1StakedBytes = citizenStatus.stakedBytes;
                        uint256 cap = VAULT_CAP;
                        if (!citizenStatus.hasVault) {
                                cap = NO_VAULT_CAP;
                        }
-                       if (citizenStatus.stakedBytes + amount > cap) {
-                               revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
+                       if (s1StakedBytes + amount > cap) {
+                               revert AmountExceedsCap(s1StakedBytes + amount, cap);
                        }

                        // Validate that the caller actually staked the Citizen.
@@ -1075,7 +1076,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        PoolData storage pool = _pools[AssetType.S1_CITIZEN];
                        unchecked {
                                uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
-                               citizenStatus.stakedBytes += amount;
+                               citizenStatus.stakedBytes = s1StakedBytes + amount;
                                citizenStatus.points += bonusPoints;
                                pool.totalPoints += bonusPoints;
                        }
@@ -1083,9 +1084,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Handle staking BYTES into an S2 Citizen.
                } else if (seasonId == 2) {
                        StakedS2Citizen storage citizenStatus = stakedS2[msg.sender][citizenId];
+                       uint256 s2StakedBytes = citizenStatus.stakedBytes;
                        uint256 cap = NO_VAULT_CAP;
-                       if (citizenStatus.stakedBytes + amount > cap) {
-                               revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
+                       if (s2StakedBytes + amount > cap) {
+                               revert AmountExceedsCap(s2StakedBytes + amount, cap);
                        }

                        // Validate that the caller actually staked the Citizen.
@@ -1096,7 +1098,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        PoolData storage pool = _pools[AssetType.S2_CITIZEN];
                        unchecked {
                                uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
-                               citizenStatus.stakedBytes += amount;
+                               citizenStatus.stakedBytes = s2StakedBytes + amount;
                                citizenStatus.points += bonusPoints;
                                pool.totalPoints += bonusPoints;
                        }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1137-L1174

### Cache `LP` and `stakerLPPosition[msg.sender].multiplier` to save 2 SLOADs.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1137:		_assetTransferFrom(LP, msg.sender, address(this), amount); // @audit: 1st sload for `LP`
1138:
1139:		// Decode the timelock option's duration and multiplier.
1140:		uint256 timelockDuration = _timelock >> 128;
1141:		uint256 timelockMultiplier = _timelock & type(uint128).max;
1142:
1143:		// If this is a new stake of this asset, initialize the multiplier details.
1144:		if (stakerLPPosition[msg.sender].multiplier == 0) { // @audit: 1st sload for `stakerLPPosition[msg.sender].multiplier`
1145:			stakerLPPosition[msg.sender].multiplier = timelockMultiplier;
1146:
1147:		// If a multiplier exists already, we must match it.
1148:		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) { // @audit: 2nd sload for `stakerLPPosition[msg.sender].multiplier`
1149:			revert MismatchedTimelock();
1150:		}
1151:
1152:		// Update caller staking information and asset data.
1153:		PoolData storage pool = _pools[AssetType.LP];
1154:		unchecked {
1155:			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
1156:
1157:			// Update the caller's LP token stake.
1158:			stakerLPPosition[msg.sender].timelockEndTime =
1159:				block.timestamp + timelockDuration;
1160:			stakerLPPosition[msg.sender].amount += amount;
1161:			stakerLPPosition[msg.sender].points += points;
1162:
1163:			// Update the pool point weights for rewards.
1164:			pool.totalPoints += points;
1165:		}
1166:
1167:		// Emit an event recording this LP staking.
1168:		emit Stake(
1169:			msg.sender,
1170:			LP, // @audit: 2nd sload for `LP`
1171:			_timelock,
1172:			amount
1173:		);
1174:	}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..3c7343e 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1134,18 +1134,20 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        contract. This transfer will fail if the caller does not hold enough
                        tokens.
                */
-               _assetTransferFrom(LP, msg.sender, address(this), amount);
+               address lp = LP;
+               _assetTransferFrom(lp, msg.sender, address(this), amount);

                // Decode the timelock option's duration and multiplier.
                uint256 timelockDuration = _timelock >> 128;
                uint256 timelockMultiplier = _timelock & type(uint128).max;

                // If this is a new stake of this asset, initialize the multiplier details.
-               if (stakerLPPosition[msg.sender].multiplier == 0) {
+               uint256 multiplier = stakerLPPosition[msg.sender].multiplier;
+               if (multiplier == 0) {
                        stakerLPPosition[msg.sender].multiplier = timelockMultiplier;

                // If a multiplier exists already, we must match it.
-               } else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) {
+               } else if (multiplier != timelockMultiplier) {
                        revert MismatchedTimelock();
                }

@@ -1167,7 +1169,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Emit an event recording this LP staking.
                emit Stake(
                        msg.sender,
-                       LP,
+                       lp,
                        _timelock,
                        amount
                );
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1264-L1396

### Cache `pool.totalPoints` to save 1 SLOAD.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1273:		PoolData storage pool = _pools[_assetType];
1274:		if (pool.totalPoints != 0) { // @audit: 1st sload
...
1276:			// Calculate the total number of points accrued to the `_recipient`.
...
1386:			// Return final shares.
1387:			unchecked {
1388:				uint256 share = points * _PRECISION / pool.totalPoints * totalReward; // @audit: 2nd sload
1389:				uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
1390:				share /= _PRECISION;
1391:				daoShare /= _PRECISION;
1392:				return ((share - daoShare), daoShare);
1393:			}
1394:		}
1395:		return (0, 0);
1396:	}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..9431d53 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1271,7 +1271,8 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        this case, do not attempt to grant any rewards so as to prevent reversion.
                */
                PoolData storage pool = _pools[_assetType];
-               if (pool.totalPoints != 0) {
+               uint256 totalPoints = pool.totalPoints;
+               if (totalPoints != 0) {

                        // Calculate the total number of points accrued to the `_recipient`.
                        uint256 points;
@@ -1385,7 +1387,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                        // Return final shares.
                        unchecked {
-                               uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
+                               uint256 share = points * _PRECISION / totalPoints * totalReward;
                                uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
                                share /= _PRECISION;
                                daoShare /= _PRECISION;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1459-L1529

### Cache `stakedCitizen.timelockEndTime`, `stakedCitizen.stakedBytes`, `stakedCitizen.stakedVaultId` and `oldPosition.length` to save 6 SLOADs.

*More SLOADs would actually be saved since storage slot access is occuring within a loop.*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1459:	function _withdrawS1Citizen () private {
1460:		uint256 citizenId;
1461:		assembly {
1462:			citizenId := calldataload(0x24)
1463:		}
1464:
1465:		// Validate that the caller has cleared their asset timelock.
1466:		StakedS1Citizen storage stakedCitizen = stakedS1[msg.sender][citizenId];
1467:		if (block.timestamp < stakedCitizen.timelockEndTime) { // @audit: 1st sload
1468:			revert TimelockNotCleared(stakedCitizen.timelockEndTime); // @audit: 2nd sload
1469:		}
1470:
1471:		// Validate that the caller actually staked this asset.
1472:		if (stakedCitizen.timelockEndTime == 0) { // @audit: 3rd sload
1473:			revert CannotWithdrawUnownedS1(citizenId);
1474:		}
1475:		
1476:		// Return any staked BYTES.
1477:		if (stakedCitizen.stakedBytes > 0) { // @audit: 1st sload
1478:			_assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes); // @audit: 2nd sload
1479:		}
1480:		
1481:		// Return any non-component Vault if one is present.
1482:		if (stakedCitizen.stakedVaultId != 0) { // @audit: 1st sload
1483:			_assetTransferFrom(
1484:				VAULT,
1485:				address(this),
1486:				msg.sender,
1487:				stakedCitizen.stakedVaultId // @audit: 2nd sload
1488:			);
1489:		}
1490:
1491:		// Return the S1 Citizen.
1492:		_assetTransferFrom(S1_CITIZEN, address(this), msg.sender, citizenId);
1493:
1494:		/*
1495:			Check each citizen ID to find its index and remove the token from the
1496:			staked item array of its old position.
1497:		*/
1498:		uint256[] storage oldPosition = _stakerS1Position[msg.sender];
1499:		for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) { // @audit: 1st sload + on every iteration
1500:
1501:			// Remove the element at the matching index.
1502:			if (citizenId == oldPosition[stakedIndex]) {
1503:				if (stakedIndex != oldPosition.length - 1) { // @audit: 2nd sload + on every iteration
1504:					oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1]; // @audit: 3rd sload + on every iteration
1505:				}
1506:				oldPosition.pop();
1507:				break;
1508:			}
1509:			unchecked { stakedIndex++; }
1510:		}
```
```diff
diff --git a/./contracts/staking/NeoTokyoStaker.sol b/./contracts/staking/NeoTokyoStakerNew.sol
index a54d218..2fa62b4 100644
--- a/./contracts/staking/NeoTokyoStaker.sol
+++ b/./contracts/staking/NeoTokyoStakerNew.sol
@@ -1464,27 +1464,30 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Validate that the caller has cleared their asset timelock.
                StakedS1Citizen storage stakedCitizen = stakedS1[msg.sender][citizenId];
-               if (block.timestamp < stakedCitizen.timelockEndTime) {
-                       revert TimelockNotCleared(stakedCitizen.timelockEndTime);
+               uint256 timelockEndTime = stakedCitizen.timelockEndTime;
+               if (block.timestamp < timelockEndTime) {
+                       revert TimelockNotCleared(timelockEndTime);
                }

                // Validate that the caller actually staked this asset.
-               if (stakedCitizen.timelockEndTime == 0) {
+               if (timelockEndTime == 0) {
                        revert CannotWithdrawUnownedS1(citizenId);
                }

                // Return any staked BYTES.
-               if (stakedCitizen.stakedBytes > 0) {
-                       _assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
+               uint256 stakedBytes = stakedCitizen.stakedBytes;
+               if (stakedBytes > 0) {
+                       _assetTransfer(BYTES, msg.sender, stakedBytes);
                }

                // Return any non-component Vault if one is present.
-               if (stakedCitizen.stakedVaultId != 0) {
+               uint256 stakedVaultId = stakedCitizen.stakedVaultId;
+               if (stakedVaultId != 0) {
                        _assetTransferFrom(
                                VAULT,
                                address(this),
                                msg.sender,
-                               stakedCitizen.stakedVaultId
+                               stakedVaultId
                        );
                }

@@ -1496,12 +1499,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        staked item array of its old position.
                */
                uint256[] storage oldPosition = _stakerS1Position[msg.sender];
-               for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
+               uint256 length = oldPosition.length;
+               for (uint256 stakedIndex; stakedIndex < length; ) {

                        // Remove the element at the matching index.
                        if (citizenId == oldPosition[stakedIndex]) {
-                               if (stakedIndex != oldPosition.length - 1) {
-                                       oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
+                               if (stakedIndex != length - 1) {
+                                       oldPosition[stakedIndex] = oldPosition[length - 1];
                                }
                                oldPosition.pop();
                                break;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1534-L1592

### Cache `stakedCitizen.timelockEndTime`, `stakedCitizen.stakedBytes`, and `oldPosition.length` as stack variables to save 5 SLOADs.

*More SLOADs would actually be saved since storage slot access is occuring within a loop.*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1542:		if (block.timestamp < stakedCitizen.timelockEndTime) { // @audit: 1st sload
1543:			revert TimelockNotCleared(stakedCitizen.timelockEndTime); // @audit: 2nd sload
1544:		}
1545:
1546:		// Validate that the caller actually staked this asset.
1547:		if (stakedCitizen.timelockEndTime == 0) { // @audit: 3rd sload
1548:			revert CannotWithdrawUnownedS2(citizenId);
1549:		}
1550:
1551:		// Return any staked BYTES.
1552:		if (stakedCitizen.stakedBytes > 0) { // @audit: 1st sload
1553:			_assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes); // @audit: 2nd sload
1554:		}
1555:
1556:		// Return the S2 Citizen.
1557:		_assetTransferFrom(S2_CITIZEN, address(this), msg.sender, citizenId);
1558:
1559:		/*
1560:			Check each citizen ID to find its index and remove the token from the
1561:			staked item array of its old position.
1562:		*/
1563:		uint256[] storage oldPosition = _stakerS2Position[msg.sender];
1564:		for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) { // @audit: 1st sload + on every iteration
1565:
1566:			// Remove the element at the matching index.
1567:			if (citizenId == oldPosition[stakedIndex]) {
1568:				if (stakedIndex != oldPosition.length - 1) { // @audit: 2nd sload + on every iteration
1569:					oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1]; // @audit: 3rd sload + on every iteration
1570:				}
1571:				oldPosition.pop();
1572:				break;
1573:			}
1574:			unchecked { stakedIndex++; }
1575:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..f975466 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1539,18 +1539,20 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Validate that the caller has cleared their asset timelock.
                StakedS2Citizen storage stakedCitizen = stakedS2[msg.sender][citizenId];
-               if (block.timestamp < stakedCitizen.timelockEndTime) {
-                       revert TimelockNotCleared(stakedCitizen.timelockEndTime);
+               uint256 timelockEndTime = stakedCitizen.timelockEndTime;
+               if (block.timestamp < timelockEndTime) {
+                       revert TimelockNotCleared(timelockEndTime);
                }

                // Validate that the caller actually staked this asset.
-               if (stakedCitizen.timelockEndTime == 0) {
+               if (timelockEndTime == 0) {
                        revert CannotWithdrawUnownedS2(citizenId);
                }

                // Return any staked BYTES.
-               if (stakedCitizen.stakedBytes > 0) {
-                       _assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
+               uint256 stakedBytes = stakedCitizen.stakedBytes;
+               if (stakedBytes > 0) {
+                       _assetTransfer(BYTES, msg.sender, stakedBytes);
                }

                // Return the S2 Citizen.
@@ -1561,12 +1563,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        staked item array of its old position.
                */
                uint256[] storage oldPosition = _stakerS2Position[msg.sender];
-               for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
+               uint256 length = oldPosition.length;
+               for (uint256 stakedIndex; stakedIndex < length; ) {

                        // Remove the element at the matching index.
                        if (citizenId == oldPosition[stakedIndex]) {
-                               if (stakedIndex != oldPosition.length - 1) {
-                                       oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
+                               if (stakedIndex != length - 1) {
+                                       oldPosition[stakedIndex] = oldPosition[length - 1];
                                }
                                oldPosition.pop();
                                break;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1605-L1644

### Cache `lpPosition.timelockEndTime`, `lpPosition.amount`, and `LP` as stack variables to save 5 SLOADs.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1605:		if (block.timestamp < lpPosition.timelockEndTime) { // @audit: 1st slod
1606:			revert TimelockNotCleared(lpPosition.timelockEndTime); // @audit: 2nd sload
1607:		}
1608:
1609:		// Validate that the caller has enough staked LP tokens to withdraw.
1610:		if (lpPosition.amount < amount) { // @audit 1st sload for `lpPosition.amount`
1611:			revert NotEnoughLPTokens(amount, lpPosition.amount); // @audit: 2nd sload for `lpPosition.amount`
1612:		}
1613:
1614:		/*
1615:			Attempt to transfer the LP tokens held in escrow by this staking contract 
1616:			back to the caller.
1617:		*/
1618:		_assetTransfer(LP, msg.sender, amount); // @audit: 1st sload for `LP`
1619:
1620:		// Update caller staking information and asset data.
1621:		PoolData storage pool = _pools[AssetType.LP];
1622:		unchecked {
1623:			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
1624:
1625:			// Update the caller's LP token stake.
1626:			lpPosition.amount -= amount; // @audit: 3rd sload for `lpPosition.amount`
1627:			lpPosition.points -= points;
1628:
1629:			// Update the pool point weights for rewards.
1630:			pool.totalPoints -= points;
1631:		}
1632:
1633:		// If all LP tokens are withdrawn, we must clear the multiplier.
1634:		if (lpPosition.amount == 0) { // @audit 4th sload for `lpPosition.amount`
1635:			lpPosition.multiplier = 0;
1636:		}
1637:
1638:		// Emit an event recording this LP withdraw.
1639:		emit Withdraw(
1640:			msg.sender,
1641:			LP, // @audit: 2nd sload for `LP`
1642:			amount
1643:		);
1644:	}
```
```diff
diff --git a/./contracts/staking/NeoTokyoStaker.sol b/./contracts/staking/NeoTokyoStakerNew.sol
index a54d218..a7c34a9 100644
--- a/./contracts/staking/NeoTokyoStaker.sol
+++ b/./contracts/staking/NeoTokyoStakerNew.sol
@@ -1602,20 +1602,23 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Validate that the caller has cleared their asset timelock.
                LPPosition storage lpPosition = stakerLPPosition[msg.sender];
-               if (block.timestamp < lpPosition.timelockEndTime) {
-                       revert TimelockNotCleared(lpPosition.timelockEndTime);
+               uin256 timelockEndTime = lpPosition.timelockEndTime;
+               if (block.timestamp < timelockEndTime) {
+                       revert TimelockNotCleared(timelockEndTime);
                }

                // Validate that the caller has enough staked LP tokens to withdraw.
-               if (lpPosition.amount < amount) {
-                       revert NotEnoughLPTokens(amount, lpPosition.amount);
+               uint256 lpAmount = lpPosition.amount;
+               if (lpAmount < amount) {
+                       revert NotEnoughLPTokens(amount, lpAmount);
                }

                /*
                        Attempt to transfer the LP tokens held in escrow by this staking contract
                        back to the caller.
                */
-               _assetTransfer(LP, msg.sender, amount);
+               address lp = LP;
+               _assetTransfer(lp, msg.sender, amount);

                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.LP];
@@ -1623,7 +1626,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

                        // Update the caller's LP token stake.
-                       lpPosition.amount -= amount;
+                       lpPosition.amount = lpAmount - amount;
                        lpPosition.points -= points;

                        // Update the pool point weights for rewards.
@@ -1631,14 +1634,14 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                }

                // If all LP tokens are withdrawn, we must clear the multiplier.
-               if (lpPosition.amount == 0) {
+               if (lpAmount == 0) {
                        lpPosition.multiplier = 0;
                }

                // Emit an event recording this LP withdraw.
                emit Withdraw(
                        msg.sender,
-                       LP,
+                       lp,
                        amount
                );
        }
```

## [G-04] Avoid emitting constants.

A log topic (declared with `indexed`) has a gas cost of `Glogtopic (375 gas)`. The [`Stake`](https://github.com/code-423n4/2023-03-https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L567neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L539) and [`Withdraw`]() events' second indexed parameter is a constant for a majority of events emitted (with the exception of the events emitted in the `_stakeLP()` and `_withdrawLP()` functions) and is unecessary to emit since the value will never change. Alternatively, you can avoid incurring the `Glogtopic (375 gas)` per call to any function that emits `Stake`/`Withdraw` (with the exception of `_stakeLP()` and `_withdrawLP()`) by creating separate events for each staking/withdraw function and opt out of using the current `indexed asset` topic in each event. This way you can still query the different staking/withdraw events and will save 375 gas for each staking/withdraw function (with the exception of `_stakeLP()` and `_withdrawLP()`). 

*Note that the events emitted in the [`_stakeLP()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1168) and [`withdrawLP()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1639) functions are not considered for this issue since the second indexed parameter is for the `LP` storage variable, which can be changed via the `configureLP()` function.*

Instances: 5

Gas Savings: `5 * 375 = 1875`

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L981-L986

*`S1_CITIZEN` is a constant*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
981:		emit Stake(
982:			msg.sender,
983:			S1_CITIZEN,
984:			_timelock,
985:			citizenId
986:		);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1033-L1038

*`S2_CITIZEN` is a constant*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1033:		emit Stake(
1034:			msg.sender,
1035:			S2_CITIZEN,
1036:			_timelock,
1037:			citizenId
1038:		);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1110-L1115

*`BYTES` is a constant*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1110:		emit Stake(
1111:			msg.sender,
1112:			BYTES,
1113:			(seasonId << 128) + citizenId,
1114:			amount
1115:		);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1524-L1528
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1524:		emit Withdraw(
1525:			msg.sender,
1526:			S1_CITIZEN,
1527:			citizenId
1528:		);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1587-L1591
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1587:		emit Withdraw(
1589:			msg.sender,
1590:			S2_CITIZEN,
1591:			citizenId
1592:		);
```

## [G-05] Multiple accesses of a mapping/array should use a storage pointer

Caching a mapping's value in a storage pointer when the value is accessed multiple times saves ~40 gas per access due to not having to perform the same offset calculation every time. Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it.

To achieve this, declare a storage pointer for the variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `_stakerS1Position[_staker]`, save its reference via a storage pointer: `uint256[] storage s1Array = _stakerS1Position[_staker]` and use the pointer instead.

Total instances: 25

Gas savings: `25 * 40 = 1000`

**Gas savings will actually be greater since storage slot offset calculation is occuring within loops in some instances**

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L715-L744

### Cache storage pointers for `_stakerS1Position[_staker]`, `_stakerS2Position[_staker]`, `stakedS1[_staker]`, and `stakedS2[_staker]`.

*More gas would actually be saved since storage slot offset calculation is occuring within a loop.*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
715:		StakedS1CitizenOutput[] memory stakedS1Details =
716:			new StakedS1CitizenOutput[](_stakerS1Position[_staker].length); // @audit: 1st access
717:		for (uint256 i; i < _stakerS1Position[_staker].length; ) { // @audit: 2nd access + on every iteration
718:			uint256 citizenId = _stakerS1Position[_staker][i]; // @audit: 3rd access + on every iteration
719:			StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId]; // @audit: accessed on every iteration
720:			stakedS1Details[i] = StakedS1CitizenOutput({
721:				citizenId: citizenId,
722:				stakedBytes: citizenDetails.stakedBytes,
723:				timelockEndTime: citizenDetails.timelockEndTime,
724:				points: citizenDetails.points,
725:				hasVault: citizenDetails.hasVault,
726:				stakedVaultId: citizenDetails.stakedVaultId
727:			});
728:			unchecked { i++; }
729:		}
730:
731:		// Compile the S2 Citizen details.
732:		StakedS2CitizenOutput[] memory stakedS2Details =
733:			new StakedS2CitizenOutput[](_stakerS2Position[_staker].length); // @audit: 1st access
734:		for (uint256 i; i < _stakerS2Position[_staker].length; ) { // @audit: 2nd access + on every iteration
735:			uint256 citizenId = _stakerS2Position[_staker][i]; // @audit: 3rd access + on every iteration
736:			StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId]; // @audit: accessed on every iteration
737:			stakedS2Details[i] = StakedS2CitizenOutput({
738:				citizenId: citizenId,
739:				stakedBytes: citizenDetails.stakedBytes,
740:				timelockEndTime: citizenDetails.timelockEndTime,
741:				points: citizenDetails.points
742:			});
743:			unchecked { i++; }
744:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..e078701 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -712,11 +712,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        ) external view returns (StakerPosition memory) {

                // Compile the S1 Citizen details.
+               uint256[] storage s1Array = _stakerS1Position[_staker];
+               mapping (uint256 => StakedS1Citizen) storage s1CitizenMap = stakedS1[_staker];
                StakedS1CitizenOutput[] memory stakedS1Details =
-                       new StakedS1CitizenOutput[](_stakerS1Position[_staker].length);
-               for (uint256 i; i < _stakerS1Position[_staker].length; ) {
-                       uint256 citizenId = _stakerS1Position[_staker][i];
-                       StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
+                       new StakedS1CitizenOutput[](s1Array.length);
+               for (uint256 i; i < s1Array.length; ) {
+                       uint256 citizenId = s1Array[i];
+                       StakedS1Citizen memory citizenDetails = s1CitizenMap[citizenId];
                        stakedS1Details[i] = StakedS1CitizenOutput({
                                citizenId: citizenId,
                                stakedBytes: citizenDetails.stakedBytes,
@@ -729,11 +731,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                }

                // Compile the S2 Citizen details.
+               uint256[] storage s2Array = _stakerS2Position[_staker];
+               mapping (uint256 => StakedS2Citizen) storage s2CitizenMap = stakedS2[_staker];
                StakedS2CitizenOutput[] memory stakedS2Details =
-                       new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
-               for (uint256 i; i < _stakerS2Position[_staker].length; ) {
-                       uint256 citizenId = _stakerS2Position[_staker][i];
-                       StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
+                       new StakedS2CitizenOutput[](s2Array.length);
+               for (uint256 i; i < s2Array.length; ) {
+                       uint256 citizenId = s2Array[i];
+                       StakedS2Citizen memory citizenDetails = s2CitizenMap[citizenId];
                        stakedS2Details[i] = StakedS2CitizenOutput({
                                citizenId: citizenId,
                                stakedBytes: citizenDetails.stakedBytes,
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1144-L1165

### Cache storage pointer for `stakerLPPosition[msg.sender]`.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1144:		if (stakerLPPosition[msg.sender].multiplier == 0) { // @audit: 1st access
1145:			stakerLPPosition[msg.sender].multiplier = timelockMultiplier; // @audit: 2nd access
1146:
1147:		// If a multiplier exists already, we must match it.
1148:		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) { // @audit: 3rd access
1149:			revert MismatchedTimelock();
1150:		}
1151:
1152:		// Update caller staking information and asset data.
1153:		PoolData storage pool = _pools[AssetType.LP];
1154:		unchecked {
1155:			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
1156:
1157:			// Update the caller's LP token stake.
1158:			stakerLPPosition[msg.sender].timelockEndTime = // @audit: 4th access
1159:				block.timestamp + timelockDuration;
1160:			stakerLPPosition[msg.sender].amount += amount; // @audit: 5th & 6th access
1161:			stakerLPPosition[msg.sender].points += points; // @audit: 7th & 8th access
1162:
1163:			// Update the pool point weights for rewards.
1164:			pool.totalPoints += points;
1165:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..7993a96 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1141,11 +1141,12 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                uint256 timelockMultiplier = _timelock & type(uint128).max;

                // If this is a new stake of this asset, initialize the multiplier details.
-               if (stakerLPPosition[msg.sender].multiplier == 0) {
-                       stakerLPPosition[msg.sender].multiplier = timelockMultiplier;
+               LPPosition storage stakerPosition = stakerLPPosition[msg.sender];
+               if (stakerPosition.multiplier == 0) {
+                       stakerPosition.multiplier = timelockMultiplier;

                // If a multiplier exists already, we must match it.
-               } else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) {
+               } else if (stakerPosition.multiplier != timelockMultiplier) {
                        revert MismatchedTimelock();
                }

@@ -1155,10 +1156,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

                        // Update the caller's LP token stake.
-                       stakerLPPosition[msg.sender].timelockEndTime =
+                       stakerPosition.timelockEndTime =
                                block.timestamp + timelockDuration;
-                       stakerLPPosition[msg.sender].amount += amount;
-                       stakerLPPosition[msg.sender].points += points;
+                       stakerPosition.amount += amount;
+                       stakerPosition.points += points;

                        // Update the pool point weights for rewards.
                        pool.totalPoints += points;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1210-L1217

### Cache storage pointer for `_pools[_assetType]`.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1210:		if (_pools[_assetType].rewardCount == 0) { // @audit: 1st access
1211:			revert UnconfiguredPool(uint256(_assetType));
1212:		}
1213:
1214:		// Validate that the asset being staked matches an active pool.
1215:		if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) { // @audit: 2nd access
1216:			revert InactivePool(uint256(_assetType));
1217:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..b81a227 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1207,12 +1207,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                }

                // Validate that the asset being staked matches a configured pool.
-               if (_pools[_assetType].rewardCount == 0) {
+               PoolData storage pool = _pools[_assetType];
+               if (pool.rewardCount == 0) {
                        revert UnconfiguredPool(uint256(_assetType));
                }

                // Validate that the asset being staked matches an active pool.
-               if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
+               if (pool.rewardWindows[0].startTime >= block.timestamp) {
                        revert InactivePool(uint256(_assetType));
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1277-L1368

### Cache storage pointers for `_stakerS1Position[_recipient]`, `_stakerS2Position[_recipient]`, `stakedS1[_recipient]`, `stakedS2[_recipient]`, and `pool.rewardWindows`.

*More gas would actually be saved since storage slot offset calculation is occuring within a loop.*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1277:			uint256 points;
1278:			if (_assetType == AssetType.S1_CITIZEN) {
1279:				for (uint256 i; i < _stakerS1Position[_recipient].length; ) { // @audit: 1st access + on every iteration
1280:					uint256 citizenId = _stakerS1Position[_recipient][i]; // @audit: 2nd access + on every iteration
1281:					StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId]; // @audit: accessed on every iteration
1282:					unchecked {
1283:						points += s1Citizen.points;
1284:						i++;
1285:					}
1286:				}
1287:			} else if (_assetType == AssetType.S2_CITIZEN) {
1288:				for (uint256 i; i < _stakerS2Position[_recipient].length; ) { // @audit: 1st access + on every iteration
1289:					uint256 citizenId = _stakerS2Position[_recipient][i]; // @audit: 2nd access + on every iteration
1290:					StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId]; // @audit: accessed on every iteration
1291:					unchecked {
1292:						points += s2Citizen.points;
1293:						i++;
1294:					}
1295:				}
1296:			} else if (_assetType == AssetType.LP) {
1297:				unchecked {
1298:					points += stakerLPPosition[_recipient].points;
1299:				}
1300:			} else {
1301:				revert InvalidAssetType(uint256(_assetType));
1302:			}
1303:
1304:			/*
1305:				Determine the reward for the `_recipient` based on their points total. 
1306:				Iterate through the entire array of pool reward windows to find the 
1307:				applicable time period.
1308:			*/
1309:			uint256 totalReward;
1310:			uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
1311:			uint256 windowCount = pool.rewardCount;
1312:			for (uint256 i; i < windowCount; ) {
1314:				RewardWindow memory window = pool.rewardWindows[i]; // @audit: 1st access + on every iteration
1315:
1316:				/*
1317:					If the last reward time is less than the starting time of this 
1318:					window, then the reward was accrued in the previous window.
1319:				*/
1320:				if (lastPoolRewardTime < window.startTime) {
1321:					uint256 currentRewardRate = pool.rewardWindows[i - 1].reward; // @audit: 2nd access + on every iteration
1322:
1323:					/*
1324:						Iterate forward to the present timestamp over any unclaimed reward 
1325:						windows.
1326:					*/
1327:					for (uint256 j = i; j < windowCount; ) {
1328:
1329:						// If the current time falls within this window, complete.
1330:						if (block.timestamp <= window.startTime) {
1331:							unchecked {
1332:								uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;
1333:								totalReward += currentRewardRate * timeSinceReward;	
1334:							}
1335:
1336:							// We have no forward goto and thus include this bastardry.
1337:							i = windowCount;
1338:							break;
1339:
1340:						// Otherwise, accrue the remainder of this window and iterate.
1341:						} else {
1342:							unchecked {
1343:								uint256 timeSinceReward = window.startTime - lastPoolRewardTime;
1344:								totalReward += currentRewardRate * timeSinceReward;
1345:							}
1346:							currentRewardRate = window.reward;
1347:							lastPoolRewardTime = window.startTime;
1348:
1349:							/*
1350:								Handle the special case of overrunning the final window by 
1351:								fulfilling the prior window and then jumping forward to use the 
1352:								final reward window.
1353:							*/
1354:							if (j == windowCount - 1) {
1355:								unchecked {
1356:									uint256 timeSinceReward =
1357:										block.timestamp - lastPoolRewardTime;
1358:									totalReward += currentRewardRate * timeSinceReward;
1359:								}
1360:	
1361:								// We have no forward goto and thus include this bastardry.
1362:								i = windowCount;
1363:								break;
1364:	
1365:							// Otherwise, iterate.
1366:							} else {
1367:								window = pool.rewardWindows[j + 1]; // @audit: 3rd access + on every iteration
1368:							}
1369:						}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..0a58815 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1276,18 +1276,22 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        // Calculate the total number of points accrued to the `_recipient`.
                        uint256 points;
                        if (_assetType == AssetType.S1_CITIZEN) {
-                               for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
-                                       uint256 citizenId = _stakerS1Position[_recipient][i];
-                                       StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
+                               uint256[] storage s1Array = _stakerS1Position[_recipient];
+                               mapping (uint256 => StakedS1Citizen) storage s1CitizenMap = stakedS1[_recipient];
+                               for (uint256 i; i < s1Array.length; ) {
+                                       uint256 citizenId = s1Array[i];
+                                       StakedS1Citizen memory s1Citizen = s1CitizenMap[citizenId];
                                        unchecked {
                                                points += s1Citizen.points;
                                                i++;
                                        }
                                }
                        } else if (_assetType == AssetType.S2_CITIZEN) {
-                               for (uint256 i; i < _stakerS2Position[_recipient].length; ) {
-                                       uint256 citizenId = _stakerS2Position[_recipient][i];
-                                       StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];
+                               uint256[] storage s2Array = _stakerS2Position[_recipient];
+                               mapping (uint256 => StakedS2Citizen) storage s2CitizenMap = stakedS2[_recipient];
+                               for (uint256 i; i < s2Array.length; ) {
+                                       uint256 citizenId = s2Array[i];
+                                       StakedS2Citizen memory s2Citizen = s2CitizenMap[citizenId];
                                        unchecked {
                                                points += s2Citizen.points;
                                                i++;
@@ -1309,15 +1313,16 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        uint256 totalReward;
                        uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
                        uint256 windowCount = pool.rewardCount;
+                       mapping (uint256 => RewardWindow) storage rewardWindowsMap = pool.rewardWindows;
                        for (uint256 i; i < windowCount; ) {
-                               RewardWindow memory window = pool.rewardWindows[i];
+                               RewardWindow memory window = rewardWindowsMap[i];

                                /*
                                        If the last reward time is less than the starting time of this
                                        window, then the reward was accrued in the previous window.
                                */
                                if (lastPoolRewardTime < window.startTime) {
-                                       uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;
+                                       uint256 currentRewardRate = rewardWindowsMap[i - 1].reward;

                                        /*
                                                Iterate forward to the present timestamp over any unclaimed reward
@@ -1363,7 +1368,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                                                        // Otherwise, iterate.
                                                        } else {
-                                                               window = pool.rewardWindows[j + 1];
+                                                               window = rewardWindowsMap[j + 1];
                                                        }
                                                }
                                                unchecked { j++; }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1432-L1435

### Cache storage pointer for `lastRewardTime[_recipient]`.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1432:		// Record the current time as the beginning time for checking rewards.
1433:		lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp; // @audit: 1st access
1434:		lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp; // @audit: 2nd access
1435:		lastRewardTime[_recipient][AssetType.LP] = block.timestamp; // @audit: 3rd access
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..4621b10 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1430,9 +1430,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                );

                // Record the current time as the beginning time for checking rewards.
-               lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;
-               lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;
-               lastRewardTime[_recipient][AssetType.LP] = block.timestamp;
+               mapping (AssetType => uint256) storage recipient = lastRewardTime[_recipient];
+               recipient[AssetType.S1_CITIZEN] = block.timestamp;
+               recipient[AssetType.S2_CITIZEN] = block.timestamp;
+               recipient[AssetType.LP] = block.timestamp;

                // Calculate total reward and tax.
                uint256 totalReward;
```

## [G-06] `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables

Total Instances: 13

Gas Savings: `13 * 20 = 260`

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L875-L987

```solidity
File: contracts/staking/NeoTokyoStaker.sol
967:		unchecked {
968:			citizenStatus.points =
969:				identityPoints * vaultMultiplier * timelockMultiplier /
970:				_DIVISOR / _DIVISOR;
971:			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
972:
973:			// Record the caller's staked S1 Citizen.
974:			_stakerS1Position[msg.sender].push(citizenId);
975:
976:			// Update the pool point weights for rewards
977:			pool.totalPoints += citizenStatus.points;
978:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..f7c4a13 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -974,7 +974,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        _stakerS1Position[msg.sender].push(citizenId);

                        // Update the pool point weights for rewards
-                       pool.totalPoints += citizenStatus.points;
+                       pool.totalPoints = pool.totalPoints + citizenStatus.points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1021-L1030

```solidity
File: contracts/staking/NeoTokyoStaker.sol
1021:		unchecked {
1022:			citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
1023:			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
1024:
1025:			// Record the caller's staked S2 Citizen.
1026:			_stakerS2Position[msg.sender].push(citizenId);
1027:
1028:			// Update the pool point weights for rewards
1029:			pool.totalPoints += citizenStatus.points;
1030:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..e940f8e 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1026,7 +1026,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        _stakerS2Position[msg.sender].push(citizenId);

                        // Update the pool point weights for rewards
-                       pool.totalPoints += citizenStatus.points;
+                       pool.totalPoints = pool.totalPoints + citizenStatus.points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1097-L1102
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1097:			unchecked {
1098:				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
1099:				citizenStatus.stakedBytes += amount;
1100:				citizenStatus.points += bonusPoints;
1101:				pool.totalPoints += bonusPoints;
1102:			}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..af3d3de 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1096,9 +1096,9 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        PoolData storage pool = _pools[AssetType.S2_CITIZEN];
                        unchecked {
                                uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
-                               citizenStatus.stakedBytes += amount;
-                               citizenStatus.points += bonusPoints;
-                               pool.totalPoints += bonusPoints;
+                               citizenStatus.stakedBytes = citizenStatus.stakedBytes + amount;
+                               citizenStatus.points = citizenStatus.points + bonusPoints;
+                               pool.totalPoints = pool.totalPoints + bonusPoints;
                        }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1154-L1165
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1154:		unchecked {
1155:			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
1156:
1157:			// Update the caller's LP token stake.
1158:			stakerLPPosition[msg.sender].timelockEndTime =
1159:				block.timestamp + timelockDuration;
1160:			stakerLPPosition[msg.sender].amount += amount;
1161:			stakerLPPosition[msg.sender].points += points;
1162:
1163:			// Update the pool point weights for rewards.
1164:			pool.totalPoints += points;
1165:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..7f52897 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1157,11 +1157,11 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        // Update the caller's LP token stake.
                        stakerLPPosition[msg.sender].timelockEndTime =
                                block.timestamp + timelockDuration;
-                       stakerLPPosition[msg.sender].amount += amount;
-                       stakerLPPosition[msg.sender].points += points;
+                       stakerLPPosition[msg.sender].amount = stakerLPPosition[msg.sender].amount + amount;
+                       stakerLPPosition[msg.sender].points = stakerLPPosition[msg.sender].points + points;

                        // Update the pool point weights for rewards.
-                       pool.totalPoints += points;
+                       pool.totalPoints = pool.totalPoints + points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1514-L1516
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1514:		unchecked {
1515:			pool.totalPoints -= stakedCitizen.points;
1516:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..89a1f6d 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1512,7 +1512,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.S1_CITIZEN];
                unchecked {
-                       pool.totalPoints -= stakedCitizen.points;
+                       pool.totalPoints = pool.totalPoints - stakedCitizen.points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1579-L1581
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1579:		unchecked {
1580:			pool.totalPoints -= stakedCitizen.points;
1581:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/./contracts/staking/NeoTokyoStaker.sol
index a54d218..f93562f 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1577,7 +1577,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.S2_CITIZEN];
                unchecked {
-                       pool.totalPoints -= stakedCitizen.points;
+                       pool.totalPoints = pool.totalPoints - stakedCitizen.points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1622-L1631
```solidity
File: contracts/staking/NeoTokyoStaking.sol
1622:		unchecked {
1623:			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
1624:
1625:			// Update the caller's LP token stake.
1626:			lpPosition.amount -= amount;
1627:			lpPosition.points -= points;
1628:
1629:			// Update the pool point weights for rewards.
1630:			pool.totalPoints -= points;
1631:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..2513548 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1623,11 +1623,11 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

                        // Update the caller's LP token stake.
-                       lpPosition.amount -= amount;
-                       lpPosition.points -= points;
+                       lpPosition.amount = lpPosition.amount - amount;
+                       lpPosition.points = lpPosition.points - points;

                        // Update the pool point weights for rewards.
-                       pool.totalPoints -= points;
+                       pool.totalPoints = pool.totalPoints - points;
                }
```

## [G-07] Use storage instead of memory for structs/arrays

If you are not returning an entire struct (all fields) in a function then it is more gas efficient to use a storage pointer rather than using a memory pointer (which copies all the values in the struct from storage into memory). If you use a memory pointer and do not access all the struct fields in your function, then you are performing unecessary sloads. With this in mind, only the [4th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L719), [6th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L736), [13th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1281), [14th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1290), and [15th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1313) instances caught by the `c4udit tool` are instances where a struct is being copied from storage into memory and therefore have the hightest potential for gas savings. Below are some clarifications regarding those instances:

- Modifying the [4th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L719) and [6th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L736) instances to use a storage pointer will not yield any substantial gas savings since all of the struct values are accessed (meaning the same amount of sloads would occur if you used a storage pointer) in the function. In addition, all the struct values are also being returned in memory. 

- Modifying the [15th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1313) instance to use a storage pointer would actually be less efficient in some cases since the struct values can potentially be read more than once during the function call. When using a memory pointer, the values would be loaded (MLOAD) from memory each time. When using a storage pointer, the values would be loaded (SLOAD) from storage each time. If you were to use a storage pointer in this instance you should consider caching the `window.startTime` and `window.reward` values to avoid re-reading those values from storage. 

- Modifying the [13th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1281) and [14th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1290) instances to use a storage pointer will result in substantial gas savings due to the fact that only **one** struct value is intended to be read during each loop. Although the function only reads one struct value from memory, the result of using a memory pointer is that ALL of the struct values are loaded from storage (5 unecessary SLOADs for the `StakedS1Citizen` struct and 2 unecessary SLOADs from the `StakedS2Citizen` struct) into memory. Using a storage pointer instead of a memory pointer for these two instances would result in the following gas savings:

### Gas savings for `stake()`, obtained via protocol's tests: Avg 4463 gas
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  154353  |  423836  |  277026  |    71   |
| After  |  149743  |  423836  |  272563  |    71   |

### Gas Savings for `withdraw()`, obtained via protocol's tests: Avg 5346 gas
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  102046  |  267114  |  187080  |    18   |
| After  |  102046  |  257972  |  181734  |    18   |



***

# Disclosures

C4 is an open organization governed by participants in the community.

C4 Contests incentivize the discovery of exploits, vulnerabilities, and bugs in smart contracts. Security researchers are rewarded at an increasing rate for finding higher-risk issues. Contest submissions are judged by a knowledgeable security researcher and solidity developer and disclosed to sponsoring developers. C4 does not conduct formal verification regarding the provided code but instead provides final verification.

C4 does not provide any guarantee or warranty regarding the security of this project. All smart contract software should be used at the sole risk and responsibility of users.
