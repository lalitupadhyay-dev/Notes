# Accounts on Solana

<img src="./imgs/flow.png" />

<br>
All data on Solana stores in accounts.
<br>
<br>

<img src="./imgs//solana-accounts.png" />

## Account address

- 32 byte address, in base58 encoded
- Most accounts use **Ed25519** algorithm to generate key

<img src="./imgs/account-address.png" />

## Account structure
Every Account has a maximum size of 10MiB (Mebibytes) equal to **2^20 bytes** and contains following information:

- **lamports**: The number of lamports in the account
- **data**: The account's data
- **owner**: The ID of the program that owns the account
- **executable**: Indicates whether the account contains executable binary
- **rent_epoch**: The deprecated rent epoch field

### 1. Lamports
- Account's balance
- Smallest unit of SOL
- Every account must have minimum lamport balance called **rent**, to store data on-chain
- Rent is proportional to size of account
- When account is closed rent is recovered

### 2. Data
- Stored data on account
- In Program account case, this field stores executable code, in form of bytes or **<span style="color: #00ffbd;">Program Account Address</span>** where the executable code is stored

### 3. Owner
- This field contains the program ID(address) of the account's owner
- This can change the data of any account, whose owner is this

### 4. Executable
- This field tells that the account is **<span style="color: #00ffbd;">Program Account</span>** or not

### 5. Rent epoch
- This field tells the rent an account need to pay for storing info

## Types of Accounts
<img src="./imgs/types-of-acc-1.png" />