# Tokens on a Blockchain (Solana-Focused Explanation)

## Overview

A token is a digital asset created and managed on top of a blockchain.
It is not the native currency of the blockchain but exists using the
blockchain’s infrastructure.

Examples:
- SOL is the native coin of Solana
- USDC, USDT, and other assets are tokens created on Solana

---

## Coin vs Token

### Coin

A coin is the native asset of a blockchain.

Characteristics:
- Used to pay transaction fees
- Used for staking or mining
- Required for network security
- Only one native coin per blockchain

Examples:
- Bitcoin → BTC
- Ethereum → ETH
- Solana → SOL

---

### Token

A token is an asset created on top of a blockchain using smart contracts
(or built-in token programs).

Characteristics:
- Not used for gas fees
- Depends on the blockchain to exist
- Can represent many different things
- Multiple tokens can exist on the same blockchain

Examples:
- USDC
- USDT

---

## Why Blockchains Allow Token Creation

Blockchains like Solana and Ethereum are programmable platforms, not just
payment networks.

Allowing token creation enables:

- Stable currencies (stablecoins)
- Decentralized finance (lending, trading, payments)
- Application-specific economies
- Governance systems
- Tokenized real-world assets

Without tokens, blockchains would be limited to simple value transfer.

---

## Why Stablecoins Exist (USDC, USDT)

Native coins like SOL or ETH are volatile in price.
This makes them unsuitable for many financial use cases.

Stablecoins are designed to:
- Maintain stable value (usually 1 USD)
- Reduce exposure to volatility
- Enable predictable payments and accounting
- Power DeFi applications

Example:
- USDC = 1 USD
- Used for trading, lending, salaries, and settlements

---

## Token Amount vs Token Type

Token:
- Refers to the asset definition (e.g., USDC)

Amount:
- Refers to how many units you own

Correct usage:
- "I have 100 USDC"

Incorrect usage:
- "I have 100 tokens of USDC"

You own a balance of a token, not multiple token definitions.

---

## How Tokens Work in Solana

In Solana, tokens are implemented using three main components:

### 1. Mint Account

Defines the token itself:
- Token name and symbol (off-chain metadata)
- Total supply
- Decimal precision
- Mint authority

Each token has exactly one mint.

---

### 2. Token Accounts

Store balances of a token for a user.

- One token account per (user, token) pair
- Stores the balance as a number
- Owned by the user’s wallet

Example:
- Wallet A owns a token account for USDC
- That account stores balance = 100

---

### 3. Token Program

A system program that:
- Creates tokens
- Transfers tokens
- Mints new supply
- Burns supply

All fungible tokens on Solana follow the same standard logic.

---

## Tokens Are Not Separate Blockchains

Tokens:
- Do not have their own consensus
- Do not have their own validators
- Do not exist without the underlying blockchain

They inherit:
- Security
- Speed

From the blockchain they are built on.

---

## Token Creation Is Permissionless

Anyone can create a token because:
- Blockchain networks are open systems
- Innovation does not require approval
- Market decides which tokens have value

This is similar to:
- Anyone can create a website on the internet
- But not every website becomes useful

---

## Summary

- Coins secure and power the blockchain
- Tokens power applications built on the blockchain
- Tokens exist because one currency cannot serve all use cases
- Stablecoins solve volatility problems
- Tokens are balances stored in accounts, not physical objects

Tokens turn a blockchain from a payment network into a financial platform.
