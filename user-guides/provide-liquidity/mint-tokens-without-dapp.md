---
description: >-
  How to Mint Swap Token and Principal Token using an atomic EIP-5792 capable
  wallet
---

# Mint Tokens (without dApp)

Provide cover by depositing [**collateral assets**](../../core-concepts/collateral-asset.md) into a [**Cork Pool**](../../core-concepts/cork-pool.md) to mint shares of Cork [**Principal Token**](../../core-concepts/principal-token.md) (cPT) and Cork [**Swap Token**](../../core-concepts/swap-token.md) (cST), without using our dApp.

This **"Mint" operation** on Cork:

* deposits [**Collateral Asset**](../../core-concepts/collateral-asset.md) into one of many [**Cork Pools**](../../core-concepts/cork-pool.md)
* mints share tokens (i.e. increases circulating supply of cPT & cST tokens of that Cork Pool)

***

## **How to Mint Shares of a Cork Pool via an Escrowed Deposit**

{% hint style="info" %}
You will be performing a trust-minimized escrowed operation. This is enforced by our escrow smart contract (CorkAdapter) to ensure that the end-user's desired intent is fulfilled properly with the expected outcomes. A deadline also ensures that your tokens are never stuck in an inconsistent state.
{% endhint %}

**Prerequisites:**

* The Cork Pool's market id (bytes32).
  * A balance of [**Collateral Asset**](../../core-concepts/collateral-asset.md) **(CA)** that pertains to this [**Cork Pool**](../../core-concepts/cork-pool.md).
* An _atomic_ [EIP-5792](https://www.eip5792.xyz/introduction) capable wallet such as SAFE Wallet by safe.global (previously Gnosis SAFE) or an "AA" Account-Abstraction Wallet.
  * For your own safety, you are advised to use a wallet that supports clear signing (i.e. some form of calldata and bundle visualization) that improves opsec.
* Completed a due diligence audit of the `safeDeposit` function on `Line 126` of the escrow manager contract (CorkAdapter) at its live deployment address via either:
  * **Sourcify.dev:** [https://repo.sourcify.dev/1/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407](https://repo.sourcify.dev/1/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407)
  * **Blockscout:** [https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=contract\_source\_code](https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=contract_source_code)
  * **Github:** [Link](https://github.com/Cork-Technology/phoenix/blob/d8d469b0189184179f64ab6e4410b1cda8fcace4/contracts/periphery/CorkAdapter.sol#L126-L168)

***

{% hint style="info" %}
The instructions below are prepared using SAFE Wallet by SAFE Labs (safe.global), but also applies to any atomic [EIP-5792](https://www.eip5792.xyz/introduction) capable wallet.
{% endhint %}

### Step 1: Open your Wallet's Transaction Builder

Before we begin, check that you’re using the latest version of your browser by visiting [whatismybrowser.com](https://www.whatismybrowser.com/). If it’s not up to date, please update your browser to the latest version before continuing.

Open the SAFE Wallet's Transaction Builder using this link: [https://app.safe.global/share/safe-app?appUrl=https%3A%2F%2Fapps-portal.safe.global%2Ftx-builder](https://app.safe.global/share/safe-app?appUrl=https%3A%2F%2Fapps-portal.safe.global%2Ftx-builder)

<details>

<summary>Alternatively, you may navigate to the Transaction Builder by following these steps:</summary>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 5.07.03 PM.png" alt="SAFE Wallet Connect Button"><figcaption><p>a.  Press [Connect] </p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 5.07.14 PM (1).png" alt="SAFE&#x27;s Connect Wallet Modal Window"><figcaption><p>b. Select your wallet</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 5.07.58 PM.png" alt="SAFE Wallet Sidebar"><figcaption><p>c. Press the [☰] hamburger button (if you do not see the sidebar), and select [New transaction].</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 5.08.21 PM.png" alt="SAFE Wallet New Transaction Modal"><figcaption><p>d. Select [Transaction Builder].</p></figcaption></figure>

</details>

***

### Step 2: Add Transaction #1 – Transfer Collateral Assets to the Escrow Contract

Add the 1st transaction to transfer collateral assets into our trust-minimized escrow manager contract (CorkAdapter):

* **Enter Address:** `eth:<ERC20 token address of Collateral Asset>`
* **To Address:** `<ERC20 token address of Collateral Asset>`
* **Contract Method Selector:** `transfer`
  * **to (escrow address):** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
  * **amount (of collateral):** \<wei amount of collateral asset to transfer/deposit>

<details>

<summary>How to obtain ERC20 token address of Collateral Asset</summary>

The [Collateral asset](../../core-concepts/collateral-asset.md) (CA) address and [Reference asset](../../core-concepts/reference-asset.md) (REF) address may be obtained by calling `CorkPoolManager.assets(poolId)` with the [Cork Pool](../../core-concepts/cork-pool.md) id, via either:

* **Etherscan (Closed source):** [**Link**](https://etherscan.io/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC#readProxyContract#F4)
* **Blockscout (Open source):** [**Link**](https://eth.blockscout.com/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC?tab=read_proxy\&source_address=0x1cCccCccCcCf9A60Fe57cd7CEf504d1DaaA78244#0x9fda5b66)
* **Foundry Command Line:**
  * `cast call`\
    `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC`\
    `"assets(bytes32)(address collateralAsset, address referenceAsset)"`\
    `<POOL_ID>`\
    `--rpc-url https://eth.drpc.org`

</details>

By following these instructions:

{% stepper %}
{% step %}
### Enter ERC20 Address of Collateral Asset

Input the Collateral Asset's ERC20 address & ABI as shown below.

* **Enter Address:** `eth:<ERC20 token address of Collateral Asset>`
* **Enter ABI:** If the ABI does not load automatically, copy and paste the ABI from [here](https://eth.blockscout.com/token/0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2?tab=contract_abi).

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.20.36 PM (1).png" alt=""><figcaption><p>Input ERC20 token address</p></figcaption></figure>
{% endstep %}

{% step %}
### Calculate the deposit amount in wei

To calculate the right wei-amount of collateral asset (CA), first obtain the token decimals of the collateral asset from its ERC20 contract address, then multiply your amount by 10<sup>^</sup>(erc20\_decimal\_of\_CA).

For example, 10 USDC (which has 6 decimals) is represented as![](data:image/gif;base64,R0lGODlhAQABAIAAAP///wAAACH5BAEAAAAALAAAAAABAAEAAAICRAEAOw==) $$10 \times 10^{6}$$ , which is `10 000 000` in wei-amount.

Use a unit converter that allows you to adjust the token decimals, such as: \
[https://converter.swiss-knife.xyz/eth](https://converter.swiss-knife.xyz/eth?wei=1000000000000000000)<br>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.21.03 PM.png" alt=""><figcaption><p>Input the amount under "Ether", adjust the decimals of "10^6", then press [Copy] button to its right.</p></figcaption></figure>
{% endstep %}

{% step %}
### Input Transaction #1 Details

* **To Address:** `<ERC20 token address of Collateral Asset>`
* **Contract Method Selector:** `transfer`
* **to (address):** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`&#x20;
* **amount (uint256):** \<wei amount of collateral asset to transfer/deposit copied from above>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.21.43 PM.png" alt=""><figcaption><p>Input transaction details under Transaction Information</p></figcaption></figure>
{% endstep %}

{% step %}
### Append Transaction #1

Press **\[+ Add new transaction]** and verify that transaction #1 has been added to the Transactions Batch.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.22.27 PM (1).png" alt=""><figcaption><p>Press [Add new transaction]</p></figcaption></figure>
{% endstep %}
{% endstepper %}

***

### Step 3: Add Transaction #2 – Request a Deposit/Mint with escrowed assets

Add the 2nd transaction to mint cPT (Cork [Principal Token](../../core-concepts/principal-token.md)) and cST (Cork [Swap Token](../../core-concepts/swap-token.md)) by depositing escrowed collateral assets into a Cork Pool:

* **Enter Address:** `eth:0x6566194141eefa99Af43Bb5Aa71460Ca2Dc90245`&#x20;
* **Enter ABI:** Copied from [here](https://eth.blockscout.com/address/0x6566194141eefa99Af43Bb5Aa71460Ca2Dc90245?tab=contract_abi)
* **To Address:** `0x6566194141eefa99Af43Bb5Aa71460Ca2Dc90245`&#x20;
* **ETH value:** `0`
* **Contract Method Selector:** `multicall`&#x20;
  * **bundle:** `[[comma separated values as below within double square brackets...]]`&#x20;
    * `"0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407"`,
      * The `to` address is the call target, which is the escrow contract address
    * `"<calldata>"`,
      * The `calldata` as copied from the [safeDeposit Step](mint-tokens-without-dapp.md#input-safedeposit-parameters).
    * `"0"`,
      * The `value` is the wei amount of ETH or native token to forward. Set to 0 (none) in this case.
    * `false`,
      * If `skipRevert` is true, other planned calls will continue executing even if this call reverts. To protect escrowed funds, we must also revert the transfer-to-escrow if the deposit fails.
    * `"0x0"`
      * The `callbackHash` should be set to the hash of the reenter bundle data. Set to 0x0 (none) in this case.

<details>

<summary><strong>Show me an example of bundle:</strong></summary>

```json
[[
"0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407",
"0x0000000000000000000000000000000000000000000000000000000000000000",
"0", 
false, 
"0x0"
]]
```

</details>

By following these instructions:

{% stepper %}
{% step %}
### Enter Address of Bundler3 contract

{% hint style="info" %}
A Bundler contract is analogous to a Router contract, with the added guarantee that each step of the execution is explicitly defined rather than implicit.

This reduces the need to absolutely verify and place trust in esoteric code when interacting with smart contracts.

By ensuring that each step is verifiable by a mainstream enduser and his wallet, this system achieves greater end-to-end transparency and security.
{% endhint %}

Input the [Bundler3 contract address](https://docs.morpho.org/get-started/resources/addresses/#bundlers) & ABI as shown below.

* **Enter Address:** `eth:0x6566194141eefa99Af43Bb5Aa71460Ca2Dc90245`&#x20;
* **Enter ABI:** If the ABI does not load automatically, copy and paste the ABI from [here](https://eth.blockscout.com/address/0x6566194141eefa99Af43Bb5Aa71460Ca2Dc90245?tab=contract_abi).

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-20 at 11.44.46 PM.png" alt=""><figcaption><p>Input Bundler3 contract address</p></figcaption></figure>
{% endstep %}

{% step %}
### Optional: Calculate the deadline in seconds since epoch

Choose a time in the future using an [epoch-converter](https://epoch-converter.swiss-knife.xyz/). We recommend setting it **at least 1 hour from now** to allow sufficient time for cosigning. Since the bundle executes atomically, time-limited ERC-20 permits are unnecessary, allowing this timestamp to be set as far in the future as desired. Use this timestamp as the `deadline` in the next step.

{% hint style="info" %}
DO NOT attempt to retry this transaction before the deadline has passed.

Retrying earlier may result in duplicate operations, unintended consequences, or loss of funds.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.31.26 PM.png" alt=""><figcaption><p>Input your desired duration from now</p></figcaption></figure>

If missing, scroll to the right of the page to see the copy button:

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-12 at 4.31.30 PM.png" alt=""><figcaption><p>Press [Copy] button</p></figcaption></figure>
{% endstep %}

{% step %}
### Input SafeDeposit Parameters

Input these parameters into the "safeDeposit" form linked [here](https://eth.blockscout.com/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407?tab=write_contract#0x41881406):

* **Contract Method:** `safeDeposit`&#x20;
  * **params:**
    * **poolId** (bytes32)
      * The cork pool market id
    * **collateralAssetsIn** (uint256)
      * The wei amount of collateral assets to deposit. ~~Use \[Max]~~ or input **the same** wei amount being transferred to escrow in [Step 2](mint-tokens-without-dapp.md#step-2-add-transaction-1-transfer-collateral-assets-to-the-escrow-contract).
    * **receiver** (address)
      * Your wallet address. Or the address to which shares (cST & cPT) will be minted.
    * **minCptAndCstSharesOut** (uint256)
      * The amount (non-denominated in wei) of Collateral Asset in [Step 2](mint-tokens-without-dapp.md#step-2-add-transaction-1-transfer-collateral-assets-to-the-escrow-contract), multiplied by 10<sup>18</sup>.
      * This guarantees the minimum amount of shares (cST & cPT) to receive.
    * **deadline** (uint256)
      * The deadline by which the transaction must be completed. Use **\[Now+1h]** or copy from an [epoch-converter](https://epoch-converter.swiss-knife.xyz/).
* Press **\[Copy calldata]**.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-20 at 10.28.39 PM.png" alt=""><figcaption><p>Input transaction details and press [Copy calldata]</p></figcaption></figure>

{% hint style="info" %}
DO NOT attempt to retry this transaction before the deadline has passed.

Retrying earlier may result in duplicate operations, unintended consequences, or loss of funds.
{% endhint %}
{% endstep %}

{% step %}
### Optional: Verify copied calldata&#x20;

Copy and paste the calldata into the Calldata Decoder linked [here](https://calldata.swiss-knife.xyz/decoder?address=0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407\&calldata=0x\&chainId=1), and press **\[Decode]** to verify your inputs:

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-21 at 2.14.07 AM.png" alt=""><figcaption><p>Verify that the values displayed are correct</p></figcaption></figure>
{% endstep %}

{% step %}
### Input Transaction #2 Details

* **To Address (**[**Bundler3**](https://docs.morpho.org/get-started/resources/addresses/#bundlers)**):** `0x6566194141eefa99Af43Bb5Aa71460Ca2Dc90245`
* **ETH value:** `0`
* **Contract Method Selector:** `multicall`&#x20;
* **bundle:** `[[comma separated values as below within double square brackets...]]`&#x20;
  * `"0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407"`,
    * The `to` address is the call target, which is the escrow contract address
  * `"<calldata>"`,
    * The `calldata` as copied from the [safeDeposit Step](mint-tokens-without-dapp.md#input-safedeposit-parameters).
  * `"0"`,
    * The `value` is the wei amount of ETH or native token to forward. Set to 0 (none) in this case.
  * `false`,
    * If `skipRevert` is true, other planned calls will continue executing even if this call reverts. To protect escrowed funds, we must also revert the transfer-to-escrow if the deposit fails.
  * `"0x0"`
    * The `callbackHash` should be set to the hash of the reenter bundle data. Set to 0x0 (none) in this case.
* **Example bundle:**

```json
[[
"0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407",
"0x0000000000000000000000000000000000000000000000000000000000000000",
"0", 
false, 
"0x0"
]]
```

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-20 at 11.24.35 PM.png" alt=""><figcaption><p>Input transaction details under Transaction Information</p></figcaption></figure>
{% endstep %}

{% step %}
### Append Transaction #2

Press **\[+ Add new transaction]** and verify that transaction #2 has been added to the Transactions Batch.

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-20 at 11.26.57 PM.png" alt=""><figcaption><p>Press [Add new transaction]</p></figcaption></figure>
{% endstep %}
{% endstepper %}

***

### Step 4: Create, Review & Simulate Transactions Batch

Press **\[Create Batch]** and follow these instructions:

{% stepper %}
{% step %}
### Review and Confirm your Batch

Make sure that you see this **"Review and Confirm"** screen.

**Important:** Enable _**atomic batch**_ if asked by your [EIP-5792](https://www.eip5792.xyz/introduction) capable wallet. Using non-atomic batch mode can lead to lost of funds!

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-21 at 12.00.02 AM.png" alt=""><figcaption><p>Ensure that there are 2 items (transfer &#x26; multicall) in the Atomic Batch</p></figcaption></figure>
{% endstep %}

{% step %}
### Verify each Transaction in your Batch

<details>

<summary>An example of each transaction in the batch:</summary>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-20 at 11.33.39 PM.png" alt=""><figcaption><p>Ensure that "method", "to" and "amount" are correct</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-20 at 11.40.03 PM.png" alt=""><figcaption><p>Ensure that "method" and "bundle" params" are correct </p></figcaption></figure>

</details>
{% endstep %}

{% step %}
### Simulate your Batch

Press **\[Simulate Batch]** and ensure that the simulated outcomes are as expected.

{% hint style="info" %}
Cork Phoenix is designed with security as a core principle, going above and beyond on eliminating security footguns:

* Our escrow smart contract immutably enforce all business invariants at every step of execution.
* The protocol is architected such that all user actions can be fully simulated and reviewed as a single atomic transaction, enabling straightforward and reliable verification before committing anything onchain. (This also applies to legacy wallets without a builtin bundler.)
* Key smart contracts have easily identifiable addresses, making review straightforward.
{% endhint %}
{% endstep %}

{% step %}
### Send your Batch

Once you are satisfied with the simulation results, press **\[Send Batch]**.
{% endstep %}

{% step %}
### Sign and Submit your Batch

&#x20;Follow the instructions from your connected wallet to sign and submit the batched transaction.&#x20;
{% endstep %}
{% endstepper %}

***

### Step 5: Multisig Process on Batched Transaction

If you are **not** using a multisig, you may skip this step.

If you are using a multisig wallet, ask each co-signer/co-owner to follow these instructions:

{% stepper %}
{% step %}
### Select the Batched Transaction in the Wallet Queue

Select the queued transaction and open the list of actions or transactions within the batch.
{% endstep %}

{% step %}
### Verify the Calldata of each transaction in the batch

Each cosigner copies the calldata of each action or transaction into a [safe decoder](https://safe.swiss-knife.xyz/calldata-decoder), and verifies that the decoded data contains the correct amounts and addresses.

DO NOT CONTINUE if they do not match!
{% endstep %}

{% step %}
### Approve the Batch

Each cosigner ensures that the batch's `messageHash` , displayed on their signing device (e.g. wallet hardware), matches completely with what is shown within the SAFE wallet app, before signing an approval of the batch.
{% endstep %}

{% step %}
### Execute the Batch

Once the threshold of cosigners have approved the batch, one of the wallet owners/cosigners has to execute the batched transaction and pay gas.
{% endstep %}
{% endstepper %}

***

### Step 6: Wait for Confirmation and Verify token balances&#x20;

Once the batched transaction is confirmed, follow your wallet-specific instructions to add both cPT (Cork [Principal Token](../../core-concepts/principal-token.md)) address and cST (Cork [Swap Token](../../core-concepts/swap-token.md)) address into your wallet to check their balances.

The cPT address and cST address may be obtained by calling `CorkPoolManager.shares(poolId)` with the [Cork Pool](../../core-concepts/cork-pool.md) id, via either:

* **Etherscan (Closed source):** [**Link**](https://etherscan.io/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC#readProxyContract#F42)
* **Blockscout (Open source):** [**Link**](https://eth.blockscout.com/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC?tab=read_proxy\&source_address=0x1cCccCccCcCf9A60Fe57cd7CEf504d1DaaA78244#0xde963ff1)
* **Foundry Command Line:**
  * `cast call`\
    `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC`\
    `"shares(bytes32)(address corkPrincipalToken, address corkSwapToken)"`\
    `<POOL_ID>`\
    `--rpc-url https://eth.drpc.org`



***
