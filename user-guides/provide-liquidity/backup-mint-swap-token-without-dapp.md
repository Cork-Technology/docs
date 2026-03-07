---
description: How to Mint Swap Tokens using an atomic EIP-5792 capable wallet
hidden: true
---

# Backup - Mint Swap Token (without dApp)

Provide cover by depositing collateral assets into a [Cork Pool](../../core-concepts/cork-pool.md) to mint shares of  Cork [Principal Token](../../core-concepts/principal-token.md) (cPT) and Cork [Swap Token](../../core-concepts/swap-token.md) (cST), without using our dApp.

This operation, called a "Mint":

* deposits [Collateral Asset](../../core-concepts/collateral-asset.md)
* mints shares (i.e. increases circulating supply of cPT & cST tokens)

## **How to Mint Shares of a Cork Pool via an Escrowed Deposit**

{% hint style="info" %}
You will be performing a trust-minimized escrowed operation. This is enforced by our escrow manager contract (CorkAdapter) to ensure that the end-user's desired intent is fulfilled properly with the expected outcomes. A deadline also ensures that your tokens are never stuck in an inconsistent state.
{% endhint %}

**Prerequisites:**

* An _atomic_ [EIP-5792](https://www.eip5792.xyz/introduction) capable wallet such as SAFE Wallet by safe.global (previously Gnosis SAFE)
* Completed a due diligence audit of the `safeDeposit` function on `Line 126` of the escrow manager contract (CorkAdapter) at its live deployment address via either:
  * **Sourcify.dev:** [https://repo.sourcify.dev/1/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407](https://repo.sourcify.dev/1/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407)
  * **Blockscout:** [https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=contract\_source\_code](https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=contract_source_code)
  * **Github:** [Link](https://github.com/Cork-Technology/phoenix/blob/d8d469b0189184179f64ab6e4410b1cda8fcace4/contracts/periphery/CorkAdapter.sol#L126-L168)
* The Cork Pool's market id (bytes32).
  * A balance of [Collateral Asset](../../core-concepts/collateral-asset.md) (CA) that pertains to this [Cork Pool](../../core-concepts/cork-pool.md).

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

### Step 2: Add Transaction #1 – Transfer Collateral Assets into Escrow Manager Contract

Add the 1st transaction to transfer collateral assets into our trust-minimized escrow manager contract (CorkAdapter):

* **Enter Address:** `eth:<ERC20 token address of Collateral Asset>`
* **To Address:** `<ERC20 token address of Collateral Asset>`
* **Contract Method Selector:** `transfer`
  * **to (escrow address):** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
  * **amount (of collateral):** \<wei amount of collateral asset to transfer/deposit>

By following these instructions:

{% stepper %}
{% step %}
### Enter Address or ENS Name of ERC20 token to deposit

Input the Collateral Asset address & ABI as shown below (prefixed with `eth:`). If the ABI does not load automatically, copy and paste the ABI from [here](https://eth.blockscout.com/token/0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2?tab=contract_abi).

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.20.36 PM (1).png" alt=""><figcaption><p>Input ERC20 token address</p></figcaption></figure>
{% endstep %}

{% step %}
### Calculate the amount in wei

To calculate the right wei-amount of collateral asset, use a unit converter such as : \
[https://converter.swiss-knife.xyz/eth](https://converter.swiss-knife.xyz/eth?wei=1000000000000000000)\
or\
[https://etherscan.io/unitconverter](https://etherscan.io/unitconverter)

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.21.03 PM.png" alt=""><figcaption><p>Press [Copy] button of "Wei"</p></figcaption></figure>
{% endstep %}

{% step %}
### Input the Transaction #1 Details

* **To Address:** `<ERC20 token address of Collateral Asset>`
* **Contract Method Selector:** `transfer`
* **to (address):** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
* **amount (uint256):** \<wei amount of collateral asset to transfer/deposit copied from above>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.21.43 PM.png" alt=""><figcaption><p>Input transaction details under Transaction Information</p></figcaption></figure>
{% endstep %}

{% step %}
### Press \[+ Add new transaction]

Verify that transaction #1 has been added to the Transactions Batch.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.22.27 PM (1).png" alt=""><figcaption><p>Press [Add new transaction]</p></figcaption></figure>
{% endstep %}
{% endstepper %}

***

### Step 3: Add Transaction #2 – Request a Deposit/Mint with escrowed assets

Add the 2nd transaction to mint cPT (Cork Principal Token) and cST (Cork Swap Token) by depositing escrowed collateral assets into a Cork Pool:

* **Enter Address:** `eth:0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
* **Enter ABI:** Copied from [here](https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=contract_abi)
* **To Address:** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`
* **Contract Method Selector:** `safeDeposit`&#x20;
  * **params:** `[comma separated values as below...]`
    * "\<poolId>",
      * The cork pool market id
    * "\<collateralAssetsIn>",
      * The amount of collateral assets to deposit.
    * "\<receiverOfShares>",
      * The address to which shares(cST & cPT) will be minted. Typically your own wallet address.
    * "\<minCptAndCstSharesOut>",
      * The minimum amount of shares(cST & cPT) to receive. Equals to collateralAssetsIn.
    * "deadline"
      * The deadline by which the transaction must be completed. From [epoch-converter](https://epoch-converter.swiss-knife.xyz/)
  * **Example params:**

```
[
"0x0000000000000000000000000000000000000000000000000000000000000000", 
"1000000000000000000", 
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

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.26.05 PM.png" alt=""><figcaption><p>Input CorkAdapter address</p></figcaption></figure>
{% endstep %}

{% step %}
### Calculate the deadline in seconds since epoch

Choose a future timestamp using an [epoch-converter](https://epoch-converter.swiss-knife.xyz/). We recommend setting it **at least 1 hour from now** to allow sufficient time for cosigning. Since the batch executes atomically, offline ERC-20 permits are unnecessary, allowing the `deadline` to be set as far in the future as desired. Use this timestamp as the `deadline` in the next step.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.31.26 PM.png" alt=""><figcaption><p>Input your desired duration from now</p></figcaption></figure>

If missing, scroll to the right of the page to see the copy button:

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.31.30 PM.png" alt=""><figcaption><p>Press [Copy] button</p></figcaption></figure>
{% endstep %}

{% step %}
### Input the Transaction #2 Details

* **To Address:** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`
* **Contract Method Selector:** `safeDeposit`&#x20;
  * **params:** `[comma separated values as below...]`
    * "\<poolId>",
      * The cork pool market id
    * "\<collateralAssetsIn>",
      * The amount of collateral assets to deposit.
    * "\<receiverOfShares>",
      * The address to which shares(cST & cPT) will be minted. Typically your own wallet address.
    * "\<minCptAndCstSharesOut>",
      * The minimum amount of shares(cST & cPT) to receive. Equals to collateralAssetsIn.
    * "deadline"
      * The deadline by which the transaction must be completed. From [epoch-converter](https://epoch-converter.swiss-knife.xyz/)

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.26.29 PM.png" alt=""><figcaption><p>Input transaction details under Transaction Information</p></figcaption></figure>
{% endstep %}

{% step %}
### Press \[+ Add new transaction]

Verify that transaction #2 has been added to the Transactions Batch.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.26.40 PM.png" alt=""><figcaption><p>Press [Add new transaction]</p></figcaption></figure>
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

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.26.52 PM (1).png" alt=""><figcaption><p>Ensure that there are 2 items in the Atomic Batch</p></figcaption></figure>
{% endstep %}

{% step %}
### Verify each transaction in your batch

<details>

<summary>An example of each transaction in the batch:</summary>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.27.47 PM.png" alt=""><figcaption><p>Ensure that "method", "to" and "amount" is correct</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.27.57 PM.png" alt=""><figcaption><p>Ensure that "to", "method", and "params" are correct </p></figcaption></figure>

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

Select the batch and open the transaction details of each transaction within the batch.
{% endstep %}

{% step %}
### Verify the Calldata of each transaction in the batch

Each cosigner copies the calldata of each transaction into a [safe decoder](https://safe.swiss-knife.xyz/calldata-decoder), and verifies that the decoded data contains the correct amounts and addresses.

DO NOT CONTINUE if they do not match!
{% endstep %}

{% step %}
### Approve the Batch

Each cosigner ensures that the batch's `messageHash` , displayed on their signing device (e.g. wallet hardware), matches completely before signing an approval of the batch.
{% endstep %}

{% step %}
### Execute the Batch

Once the threshold of cosigners have approved the batch, one of the wallet owners/signers has to execute the transaction (using the wallet) and pay gas.
{% endstep %}
{% endstepper %}

***

### Step 6: Wait for Confirmation and Verify token balances&#x20;

Once the batch transaction is confirmed, follow your wallet-specific instructions to add both cPT (Cork [Principal Token](../../core-concepts/principal-token.md)) address and cST (Cork [Swap Token](../../core-concepts/swap-token.md)) address into your wallet to check their balances.

Using the [Cork Pool](../../core-concepts/cork-pool.md) id, the cPT address and cST address may be obtained by calling `CorkPoolManager.shares(poolId)` using either:

* **Etherscan Website:** [**Link**](https://etherscan.io/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC#readProxyContract#F42)
* **Blockscout Website:** [**Link**](https://eth.blockscout.com/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC?tab=read_proxy\&source_address=0x1cCccCccCcCf9A60Fe57cd7CEf504d1DaaA78244#0xde963ff1)
* **Foundry Command Line:**
  * `cast call`\
    `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC`\
    `"shares(bytes32)(address corkPrincipalToken, address corkSwapToken)"`\
    `<POOL_ID>`\
    `--rpc-url https://eth.drpc.org`



***
