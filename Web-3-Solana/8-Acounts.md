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
- This can modify the data of any account, whose owner is this
- Cannot be null

### 4. Executable
- This field tells that the account is **<span style="color: #00ffbd;">Program Account</span>** or not

### 5. Rent epoch
- This field tells the rent an account need to pay for storing info

## Types of Accounts
<br>

- <span style="color: #00ffbd;">**Note: All types of accounts must have an owner**</span>, otherwise <span style="color: red;">**error**</span> will be thrown while account creation time
<br>
<br>

<img src="./imgs/types-of-acc-1.png" />

### Program Accounts

<img src="./imgs/program-account.png" /><br>

- Used to hold programs/smart-contracts code, their **executable** field is marked as <span style="color: #2067db;">**true**</span>
- Every Program Account is owned by <span style="color: #00ffbd;">**Loader Program**</span> which is used to deploy and manage the program

    - Few things about <span style="color: #00ffbd;">**Loader Program**</span>:
        - Deploys program code, by creating program account
        - Marks program as executable
        - Loads bytecode into runtime memory
        - Handles program upgrades

- A Program Account does not directly store the **data** or **executable code**
- A <span style="color: #00ffbd;">**Program Data Account**</span> stores the executable code
<br>
<br>
<img src="./imgs/program-data-account.png" />

### Data Accounts / Program State Accounts

- Do not contain executable code

    - To create a <span style="color: #00ffbd;">**Program State Account**</span>:
        - Invoke <span style="color: #00ffbd;">**System Program**</span> to create the account
        - After creation of new account the ownership is transferred to a new program
        - Account data gets initialized

<br>

<img src="./imgs/data-account.png" />

<br>

- BPF loader creates a Program Account -> Program Data Account -> Program code is stored
- When Program executes a Data account is created by **System Program**
- Then data is stored in this account