- [Low](#low)
    - [**1. Lack of address checks**](#1-lack-of-checks-address0)
    - [**2. Lack of event emit**](#2-lack-of-event-emit)
    - [**3. Unsafe transfer**](#3-unsafe-transfer)
- [Non critical](#non-critical)
    - [**4. Typo**](#4-typo)
    - [**5. Avoid hardcoded values**](#5-avoid-hardcoded-values)

# Low

## **1. Lack of checks `address(0)`**

The following methods have a lack of checks if the received argument is an address, it's good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than `address(0)`.

**Affected source code:**

- [BYTES2.sol:76-79](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L76-L79)
- [NeoTokyoStaker.sol:589-594](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L589-L594)

## **2. Lack of event emit**

The `changeStakingContractAddress` and `changeTreasuryContractAddress` methods do not emit an event when the state changes, something that it's very important for dApps and users.

**Affected source code:**

- [BYTES2.sol:165](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L165)
- [BYTES2.sol:176](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/BYTES2.sol#L176)

## **3. Unsafe transfer**

It does not check the return returned in the case of ERC-20, so a token that returns false could incur economic losses for the user or the project.

It is necessary to check that if `data` is a boolean, it is positive, just like Open Zeppelin's SafeTransfer does.

**Affected source code:**

- [NeoTokyoStaker.sol:755-786](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L755-L786)
- [NeoTokyoStaker.sol:796-814](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L796-L814)

---

# Non critical

## **4. Typo**

Tab was used instead of a space:

> S2) and BYTES for	time-locked emission rewards. The staker operates on a 

**Affected source code:**

- [NeoTokyoStaker.sol:181](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L181)

## **5. Avoid hardcoded values**

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code.

**Affected source code:**

Use the selector instead of the raw value:

- [NeoTokyoStaker.sol:191](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L191)
- [NeoTokyoStaker.sol:194](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L194)
