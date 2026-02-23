# Programs on Solana

- It is a <span style="color: #00ffbd;">**stateless**</span> account.
- These are Smart Contracts or executable code, that runs on chain.

## Types of Programs

### 1. System Program

- Only program to create new accounts
- By default the owner is this program
- Functions of System Program
    - **Create** an account
    - **Allocate** space (bytes) for data in account
    - **Assign** program ownership to accounts
    - **Transfer** SOL
- Address of the system program is <code style="color: #dfdfdf">11111111111111111111111111111111</code>.

### 2. Token Program

- Token program is one of many programs maintained by solana under solana programming library
- The token program has logic/functions that lets users
    - Create their own token (create a new mint).
    - Mint new tokens (to themselves, or other users).
    - Allows users to transfer tokens amongst themselves.
    - Allows admins to freeze tokens.




