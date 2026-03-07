---
description: >-
  How to Withdraw Collateral Assets early using an atomic EIP-5792 capable
  wallet
hidden: true
---

# Unwind Tokens (without dApp)

Withdraw collateral assets from a [Cork Pool](../../core-concepts/cork-pool.md) before its expiry, by performing a redemption of both Cork [Principal Token](../../core-concepts/principal-token.md) and Cork [Swap Token](../../core-concepts/swap-token.md), without using our dApp.

This operation, called an "Unwind Mint":

* returns and burns shares (i.e. reduces circulating supply of cPT & cST tokens)
* withdraws [Collateral Asset](../../core-concepts/collateral-asset.md)

## **How to Redeem Shares via an Escrowed Unwind-Mint**

{% hint style="info" %}
You will be performing a trust-minimized escrowed operation. This is enforced by our escrow manager contract (CorkAdapter) to ensure that the end-user's desired intent is fulfilled properly with the expected outcomes. A deadline also ensures that your tokens are never stuck in an inconsistent state.
{% endhint %}

**Prerequisites:**

* An _atomic_ [EIP-5792](https://www.eip5792.xyz/introduction) capable wallet such as SAFE Wallet by safe.global (previously Gnosis SAFE)
* Completed a due diligence audit of the `safeUnwindMint` function on `Line 216` of the escrow manager contract (CorkAdapter) at its live deployment address via either:
  * **Sourcify.dev:** [https://repo.sourcify.dev/1/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407](https://repo.sourcify.dev/1/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407)
  * **Blockscout:** [https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=contract\_source\_code](https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=contract_source_code)
  * **Github:** [Link](https://github.com/Cork-Technology/phoenix/blob/d8d469b0189184179f64ab6e4410b1cda8fcace4/contracts/periphery/CorkAdapter.sol#L216-L266)
* The Cork Pool's market id (bytes32).
  * A balance of Cork [Principal Token](../../core-concepts/principal-token.md) (cPT) and Cork [Swap Token](../../core-concepts/swap-token.md) (cST) that pertains to this [Cork Pool](../../core-concepts/cork-pool.md).

***

{% hint style="info" %}
The instructions below are prepared using SAFE Wallet by SAFE Labs (safe.global), but also applies to any atomic [EIP-5792](https://www.eip5792.xyz/introduction) capable wallet.
{% endhint %}

### Step 1: Open your Wallet's Transaction Builder

Open the SAFE Wallet's Transaction Builder using this link: [https://app.safe.global/share/safe-app?appUrl=https%3A%2F%2Fapps-portal.safe.global%2Ftx-builder](https://app.safe.global/share/safe-app?appUrl=https%3A%2F%2Fapps-portal.safe.global%2Ftx-builder)

<details>

<summary>Alternatively, you may navigate to the Transaction Builder by following these steps:</summary>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 5.07.03 PM.png" alt="SAFE Wallet Connect Button"><figcaption><p>a.  Press [Connect] </p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 5.07.14 PM (1).png" alt="SAFE&#x27;s Connect Wallet Modal Window"><figcaption><p>b. Select your wallet</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 5.07.58 PM.png" alt="SAFE Wallet Sidebar"><figcaption><p>c. Press the [☰] hamburger button (if you do not see the sidebar), and select [New transaction].</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 5.08.21 PM.png" alt="SAFE Wallet New Transaction Modal"><figcaption><p>d. Select [Transaction Builder].</p></figcaption></figure>

</details>

***

### Step 2: Add Transaction #1 – Allow spending of Principal Tokens by Escrow Manager Contract

Add the 1st transaction to allow the trust-minimized escrow manager contract (CorkAdapter) to spend your Cork Principal Tokens:

* **Enter Address:** `eth:<ERC20 token address of Cork Principal Token>`
* **Enter ABI:** Copy from [here](https://eth.blockscout.com/token/0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2?tab=contract_abi)
* **To Address:** `<ERC20 token address of Cork Principal Token>`
* **Contract Method Selector:** `approve`
  * **spender (escrow contract):** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
  * **amount (of cPT):** \<wei amount of principal tokens to redeem>

By following these instructions:

{% stepper %}
{% step %}
### Enter Address of Cork Principal Token to redeem

Cork [Principal Token](../../core-concepts/principal-token.md) (cPT) represents a share of the [Cork Pool](../../core-concepts/cork-pool.md).

Using the [Cork Pool](../../core-concepts/cork-pool.md) id, the cPT address and cST address may be obtained by calling `CorkPoolManager.shares(poolId)` using either:

* **Etherscan Website:** [**Link**](https://etherscan.io/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC#readProxyContract#F42)
* **Blockscout Website:** [**Link**](https://eth.blockscout.com/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC?tab=read_proxy\&source_address=0x1cCccCccCcCf9A60Fe57cd7CEf504d1DaaA78244#0xde963ff1)
* **Foundry Command Line:**
  * `cast call`\
    `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC`\
    `"shares(bytes32)(address corkPrincipalToken, address corkSwapToken)"`\
    `<POOL_ID>`\
    `--rpc-url https://eth.drpc.org`

Input the cPT address & ABI as shown below (prefixed with `eth:`). If the ABI does not load automatically, copy and paste the ABI from [here](https://eth.blockscout.com/token/0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2?tab=contract_abi).

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 6.20.09 PM.png" alt=""><figcaption><p>Input cPT address &#x26; ABI</p></figcaption></figure>
{% endstep %}

{% step %}
### Calculate the amount in wei

Cork Principal Tokens have 18 decimals (i.e. 10^18 precision). To calculate the right wei-amount of principal tokens, use a unit converter such as: \
[https://converter.swiss-knife.xyz/eth](https://converter.swiss-knife.xyz/eth?wei=1000000000000000000)\
or\
[https://etherscan.io/unitconverter](https://etherscan.io/unitconverter)

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.21.03 PM.png" alt=""><figcaption><p>Press [Copy] button of "Wei"</p></figcaption></figure>
{% endstep %}

{% step %}
### Input the Transaction #1 Details

* **To Address:** `<ERC20 token address of Cork Principal Token>`
* **Contract Method Selector:** `approve`
* **spender (address):** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
* **amount (uint256):** \<wei amount of cPT to redeem copied from above>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 6.24.07 PM.png" alt=""><figcaption><p>Input transaction details under Transaction Information</p></figcaption></figure>
{% endstep %}

{% step %}
### Press \[+ Add new transaction]

Verify that transaction #1 has been added to the Transactions Batch.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 6.30.57 PM.png" alt=""><figcaption><p>Press [Add new transaction]</p></figcaption></figure>
{% endstep %}
{% endstepper %}

***

### Step 3: Add Transaction #2 – Allow spending of Swap Tokens by Escrow Manager Contract

Add the 2nd transaction to allow the trust-minimized escrow manager contract (CorkAdapter) to spend your Cork Swap Tokens:

* **Enter Address:** `eth:<ERC20 token address of Cork Swap Token>`&#x20;
* **Enter ABI:** Copy from [here](https://eth.blockscout.com/token/0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2?tab=contract_abi)
* **To Address:** `<ERC20 token address of Cork Swap Token>`
* **Contract Method Selector:** `approve`
  * **spender (escrow contract):** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
  * **amount (of cST):** \<wei amount of swap tokens to redeem>

By following these instructions:

{% stepper %}
{% step %}
### Obtain the Cork Swap Token address

Cork Swap Token (cST) is paired with their corresponding Cork Principal Token (cPT).

Using the [Cork Pool](../../core-concepts/cork-pool.md) id, the cPT address and cST address may be obtained by calling `CorkPoolManager.shares(poolId)` using either:

* **Etherscan Website:** [**Link**](https://etherscan.io/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC#readProxyContract#F42)
* **Blockscout Website:** [**Link**](https://eth.blockscout.com/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC?tab=read_proxy\&source_address=0x1cCccCccCcCf9A60Fe57cd7CEf504d1DaaA78244#0xde963ff1)
* **Foundry Command Line:**
  * `cast call`\
    `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC`\
    `"shares(bytes32)(address corkPrincipalToken, address corkSwapToken)"`\
    `<POOL_ID>`\
    `--rpc-url https://eth.drpc.org`
{% endstep %}

{% step %}
### Enter Address of Cork Swap Token to redeem

Input the cST address & ABI as shown below (prefixed with `eth:`). If the ABI does not load automatically, copy and paste the ABI from [here](https://eth.blockscout.com/token/0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2?tab=contract_abi).

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 6.20.09 PM.png" alt=""><figcaption><p>Input cST address &#x26; ABI</p></figcaption></figure>
{% endstep %}

{% step %}
### Input the Transaction #2 Details

* **To Address:** `<ERC20 token address of Cork Swap Token>`
* **Contract Method Selector:** `approve`
* **spender (address):** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
* **amount (uint256):** \<wei amount of cST to redeem, copied from the wei amount of cPT in the previous step>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 6.24.07 PM.png" alt=""><figcaption><p>Input transaction details under Transaction Information</p></figcaption></figure>
{% endstep %}

{% step %}
### Press \[+ Add new transaction]

Verify that transaction #2 has been added to the Transactions Batch.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 7.02.05 PM.png" alt=""><figcaption><p>Press [Add new transaction]</p></figcaption></figure>
{% endstep %}
{% endstepper %}

***

### Step 4: Add Transaction #3 – Request an escrowed Unwind-Mint

Add the 3rd transaction to withdraw collateral assets from a Cork Pool by redeeming both cPT (Cork Principal Token) and cST (Cork Swap Token):

* **Enter Address:** `eth:0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
* **Enter ABI:** Copied from [here](https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=contract_abi)
* **To Address:** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`
* **Contract Method Selector:** `safeUnwindMint`&#x20;
  * **params:** `[comma separated values as below...]`
    * "\<poolId>",
      * The cork pool market id
    * "\<cptAndCstSharesIn>",
      * The wei amount of shares to redeem. Copied from the previous step.
    * "\<ownerAddress>",
      * Your wallet address (or the address that owns the cPT and cST shares to be burned).
    * "\<receiverAddress>",
      * Your wallet address (or the address to which collateral assets and any excess shares will be sent).
    * "\<minCollateralAssetsOut>",
      * The minimum wei amount of collateral assets to receive from the unwind operation. From [unit-converter.](https://converter.swiss-knife.xyz/eth?wei=1000000000000000000)
    * "\<deadline>"
      * The numeric deadline by which the transaction must be completed. From [epoch-converter](https://epoch-converter.swiss-knife.xyz/).
  * **Example params:**

```
[
"0x0000000000000000000000000000000000000000000000000000000000000000", 
"1000000000000000000", 
"0xd01F187CFBB86b0422536814f58f43d1806D49Cf", 
"0xd01F187CFBB86b0422536814f58f43d1806D49Cf", 
"1000000000000000000", 
"1768293967"
]
```

By following these instructions:

{% stepper %}
{% step %}
### Enter Address or ENS Name of CorkAdapter address

Input the CorkAdapter contract address & ABI as shown below. If the ABI does not load automatically, copy and paste the ABI from [here](https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=contract_abi).&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 7.22.01 PM.png" alt=""><figcaption><p>Input CorkAdapter address</p></figcaption></figure>
{% endstep %}

{% step %}
### Calculate the deadline in seconds since epoch

Choose a future timestamp using an [epoch-converter](https://epoch-converter.swiss-knife.xyz/). We recommend setting it **at least 1 hour from now** to allow sufficient time for cosigning. Since the batch executes atomically, offline ERC-20 permits are unnecessary, allowing this `deadline` to be set as far in the future as desired. Use this timestamp as the `deadline` in the next step.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.31.26 PM.png" alt=""><figcaption><p>Input your desired duration from now</p></figcaption></figure>

If missing, scroll to the right of the page to see the copy button:

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.31.30 PM.png" alt=""><figcaption><p>Press [Copy] button</p></figcaption></figure>
{% endstep %}

{% step %}
### Input the Transaction #3 Details

* **To Address:** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`
* **Contract Method Selector:** `safeUnwindMint`&#x20;
  * **params:** `[comma separated values as below...]`
    * "\<poolId>",
      * The cork pool market id
    * "\<cptAndCstSharesIn>",
      * The wei amount of shares to redeem. Copied from the previous step.
    * "\<ownerAddress>",
      * Your wallet address (or the address that owns the cPT and cST shares to be burned).
    * "\<receiverAddress>",
      * Your wallet address (or the address to which collateral assets and any excess shares will be sent).
    * "\<minCollateralAssetsOut>",
      * The minimum wei amount of collateral assets to receive from the unwind operation. From [unit-converter.](https://converter.swiss-knife.xyz/eth?wei=1000000000000000000)
    * "\<deadline>"
      * The numeric deadline by which the transaction must be completed. From [epoch-converter](https://epoch-converter.swiss-knife.xyz/).

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 7.25.05 PM.png" alt=""><figcaption><p>Input transaction details under Transaction Information</p></figcaption></figure>
{% endstep %}

{% step %}
### Press \[+ Add new transaction]

Verify that transaction #3 has been added to the Transactions Batch.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 7.25.31 PM.png" alt=""><figcaption><p>Press [Add new transaction]</p></figcaption></figure>
{% endstep %}
{% endstepper %}

***

### Step 4: Create, Review & Simulate Batch

Press \[Create Batch] and follow these instructions:

{% stepper %}
{% step %}
### Review and Confirm batch

Make sure that you see this "Review and Confirm" screen.

**Important:** Enable _**atomic batch**_ if asked by your [EIP-5792](https://www.eip5792.xyz/introduction) capable wallet. Using non-atomic batch mode can lead to lost of funds!

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 7.29.12 PM.png" alt=""><figcaption><p>Ensure that there are 3 items in the Atomic Batch</p></figcaption></figure>
{% endstep %}

{% step %}
### Verify each transaction in your batch

<details>

<summary>An example of each transaction in the batch:</summary>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 7.30.04 PM.png" alt=""><figcaption><p>Ensure that "method", "spender" and "amount" is correct</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 7.30.44 PM.png" alt=""><figcaption><p>Ensure that "method", "spender" and "amount" is correct</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-19 at 7.31.42 PM.png" alt=""><figcaption><p>Ensure that "to", "method", and "params" are correct </p></figcaption></figure>

</details>
{% endstep %}

{% step %}
### Simulate the batch

Press \[Simulate Batch] and ensure that the simulated outcomes are as expected.
{% endstep %}

{% step %}
### Send Batch

Once the simulation has completed successfully, press \[Send Batch].
{% endstep %}
{% endstepper %}

***

### Step 5: Sign and Execute transaction

Ask each co-signer/co-owner to follow these instructions:

{% stepper %}
{% step %}
### Select the Batch in the Wallet Queue

Select the batch and open the details of each transaction within the batch.
{% endstep %}

{% step %}
### Verify the Calldata of each transaction in the batch

Each cosigner copies the calldata of each transaction into a [safe decoder](https://safe.swiss-knife.xyz/calldata-decoder), and verifies that the decoded data contains the correct amounts and addresses.

DO NOT CONTINUE if they do not match!
{% endstep %}

{% step %}
### Approve the Batch

Each cosigner ensures that the batch's `safeTxHash` (or EIP-712  `messageHash`), displayed on their signing device (e.g. wallet hardware), matches completely before signing an approval of the batch.
{% endstep %}

{% step %}
### Execute the Batch

Once the threshold of cosigners have approved the batch, one of the wallet owners/signers has to execute the transaction (using the wallet) and pay gas.
{% endstep %}
{% endstepper %}

***

### Step 6: Wait for Confirmation and Verify token balances&#x20;

Once the batch transaction is confirmed, follow your wallet-specific instructions to check your collateral asset balance.

Using the [Cork Pool](../../core-concepts/cork-pool.md) id, the [Collateral asset ](../../core-concepts/collateral-asset.md)(CA) address and [Reference asset](../../core-concepts/reference-asset.md) (REF) address may be obtained by calling `CorkPoolManager.assets(poolId)` using either:

* **Etherscan Website:** [**Link**](https://etherscan.io/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC#readProxyContract#F4)
* **Blockscout Website:** [**Link**](https://eth.blockscout.com/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC?tab=read_proxy\&source_address=0x1cCccCccCcCf9A60Fe57cd7CEf504d1DaaA78244#0x9fda5b66)
* **Foundry Command Line:**
  * `cast call`\
    `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC`\
    `"assets(bytes32)(address collateralAsset, address referenceAsset)"`\
    `<POOL_ID>`\
    `--rpc-url https://eth.drpc.org`



***
