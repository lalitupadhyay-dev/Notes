# Creating Account on Solana using Solana CLI for Devnet

## Step 1: Generate a Key-Pair
- Open CMD and type following command

    ```js 
    solana-keygen new
    ```
    <img src="./imgs/CA-step-1.png" style="border: 2px solid #fff; border-radius: 10px" />
    
    <img src="./imgs/CA-step-1-1.png" style="border: 2px solid #fff; border-radius: 10px; width: 500px; display: block; margin: 2rem auto;" />

- If key-pair is generated, then you will see the following output

    <img src="./imgs/CA-step-1-2.png" style="border: 2px solid #fff; border-radius: 10px; display: block; margin: 2rem auto;" />

## Step 2: Airdrop some SOL 

- Link: <a href="https://faucet.solana.com">faucet.solana.com</a>

    <img src="./imgs/CA-step-2.png" style="border: 2px solid #fff; border-radius: 10px; width: 300px;" />

<br>

- Check the config for RPC URL (should be Devnet)

    <img src="./imgs/CA-step-2-1.png" style="border: 2px solid #fff; border-radius: 10px;" />
<br>

- If not set to <span style="color: #00ffbd;">**Devnet**</span>, then type this command in CMD

    <img src="./imgs/CA-step-2-2.png" style="border: 2px solid #fff; border-radius: 10px;" />

<br>

- After connecting to <span style="color: #00ffbd;">**Devnet**</span>, check the balance

    ``` js
    solana balance
    ```

    <img src="./imgs/CA-step-2-3.png" style="border: 2px solid #fff; border-radius: 10px;" />

## Step 3: Create Token Mint

- Type below command in CMD

    ``` js
    spl-token create-token
    ```
    <br>
    <img src="./imgs/CA-step-3.png" style="border: 2px solid #fff; border-radius: 10px;" />
<br>

- View it on <a href="https://explorer.solana.com/">explorer.solana.com</a>

    <img src="./imgs/CA-step-3-1.png" style="border: 2px solid #fff; border-radius: 10px;" />

<br>

- How the <span style="color: #00ffbd;">**Token Mint Account**</span> is created?

    - First <span style="color: #00ffbd;">**System Program**</span> creates the account

        <img src="./imgs/CA-step-3-2.png" style="border: 2px solid #fff; border-radius: 10px;" />
    <br>

    - Then, a CPI (Cross Program Invocation) happens and <span style="color: #00ffbd;">**Token Program**</span> initializes the mint

        <img src="./imgs/CA-step-3-3.png" style="border: 2px solid #fff; border-radius: 10px;" />
    <br>

    - Then, <span style="color: #00ffbd;">**Compute Budget Program**</span> calculates Gas Fees

        <img src="./imgs/CA-step-3-4.png" style="border: 2px solid #fff; border-radius: 10px;" />
    <br>

## Step 4: Create ATA (Associated Token Account) to hold Token for myself

- Write following command in CMD

    ``` js
    spl-token create-account 5XAHX6xB15fBYvj3TqkiebM7KknDdrAbeBrLP6ZzQwtZ
      "cli"     "function"             "Token Mint Address"
    ```
<br>

- The ATA for this Token Mint is created

    <img src="./imgs/CA-step-4-1.png" style="border: 2px solid #fff; border-radius: 10px;" />
<br>

- View on <a href="https://explorer.solana.com/">explorer.solana.com</a>

    <img src="./imgs/CA-step-4.png" style="border: 2px solid #fff; border-radius: 10px;" />

<br>

- How the <span style="color: #00ffbd;">**Associated Token Account**</span> is created?

    - <span style="color: #00ffbd;">**Associated Token Program**</span> creates the **Associated Token Account**

        <img src="./imgs/CA-step-4-2.png" style="border: 2px solid #fff; border-radius: 10px;" />

