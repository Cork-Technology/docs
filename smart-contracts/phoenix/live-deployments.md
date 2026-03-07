---
description: Official live deployment addresses of Cork smart contracts
hidden: true
---

# Live Deployments

We are officially live on these networks:

{% tabs %}
{% tab title="Ethereum" %}
## Ethereum Mainnet

#### **EVM Chain**

* **ChainID:** 1 (0x1)
  * One-click add the Etherum mainnet and find list of recent RPC endpoints here:\
    [https://chainlist.org/chain/1](https://chainlist.org/chain/1)
* **Explorers:**
  * [https://etherscan.io/](https://etherscan.io/)
  * [https://eth.blockscout.com/ ](https://eth.blockscout.com/)

***

### Main Contract Addresses (Ethereum)

<table><thead><tr><th width="138">Name</th><th width="381">Deployment Address</th><th>Labels<select multiple><option value="yAvnMI9UjzJF" label="AMM" color="blue"></option><option value="Z1pjjMq809JL" label="Entrypoint" color="blue"></option><option value="Pl5VmPYwCQgM" label="PSM" color="blue"></option><option value="mwlRALSuocDX" label="Vault" color="blue"></option><option value="qMDqikZPGzUu" label="Singleton" color="blue"></option><option value="ORBYwe8RAMiY" label="Factory" color="blue"></option><option value="3IKIvoG6V7jv" label="ERC20-Factory" color="blue"></option><option value="YvwdLsHDGSrX" label="Trading" color="blue"></option><option value="tWKUji7R5Y6n" label="Open-Market" color="blue"></option><option value="MUEjzrobBiZt" label="Creating" color="blue"></option></select></th></tr></thead><tbody><tr><td><strong>Cork Config</strong></td><td><a href="https://eth.blockscout.com/address/0xF0DA8927Df8D759d5BA6d3d714B1452135D99cFC?tab=contract"><code>0xF0DA8927Df8D759d5BA6d3d714B1452135D99cFC</code></a></td><td><span data-option="Z1pjjMq809JL">Entrypoint, </span><span data-option="MUEjzrobBiZt">Creating</span></td></tr><tr><td><strong>Cork Flashswap Router</strong></td><td><a href="https://eth.blockscout.com/address/0x55B90B37416DC0Bd936045A8110d1aF3B6Bf0fc3?tab=contract"><code>0x55B90B37416DC0Bd936045A8110d1aF3B6Bf0fc3</code></a></td><td><span data-option="Z1pjjMq809JL">Entrypoint, </span><span data-option="YvwdLsHDGSrX">Trading</span></td></tr><tr><td><strong>Cork PSM &#x26;</strong> <br><strong>Cork Vault</strong> <br><strong>[ModuleCore]</strong></td><td><a href="https://eth.blockscout.com/address/0xCCd90F6435dd78C4ECCED1FA4db0D7242548a2a9?tab=contract"><code>0xCCd90F6435dd78C4ECCED1FA4db0D7242548a2a9</code></a></td><td><span data-option="Pl5VmPYwCQgM">PSM, </span><span data-option="mwlRALSuocDX">Vault, </span><span data-option="qMDqikZPGzUu">Singleton</span></td></tr><tr><td><strong>Cork  UniswapV4 Hook</strong></td><td><a href="https://eth.blockscout.com/address/0x5287E8915445aee78e10190559D8Dd21E0E9Ea88?tab=contract"><code>0x5287E8915445aee78e10190559D8Dd21E0E9Ea88</code></a></td><td><span data-option="yAvnMI9UjzJF">AMM, </span><span data-option="3IKIvoG6V7jv">ERC20-Factory</span></td></tr></tbody></table>

### Token Factory Contract Addresses (Ethereum)

{% hint style="info" %}
These are only **factory** and **implementation** addresses for auditors.

Users should use market-specific ERC20 token addresses, which are available from:

* the Dapp interface at [https://app.cork.tech](https://app.cork.tech), or
* the `Issued` events or `InitializedModuleCore` events emitted onchain by the `ModuleCore` contract at [`0xCCd90F6435dd78C4ECCED1FA4db0D7242548a2a9`](https://eth.blockscout.com/address/0xCCd90F6435dd78C4ECCED1FA4db0D7242548a2a9?tab=contract).
{% endhint %}



<table><thead><tr><th width="140">Name</th><th width="384">Deployment Address</th><th>Labels<select multiple><option value="yAvnMI9UjzJF" label="AMM" color="blue"></option><option value="Z1pjjMq809JL" label="Entrypoint" color="blue"></option><option value="Pl5VmPYwCQgM" label="PSM" color="blue"></option><option value="mwlRALSuocDX" label="Vault" color="blue"></option><option value="k7PAV43g5UEQ" label="Implementation" color="blue"></option><option value="gST6gp8cSJx9" label="ERC20" color="blue"></option><option value="RH7GcyEc5ziF" label="Dummy" color="blue"></option><option value="cClOkU0JUeqH" label="ERC20-Factory" color="blue"></option></select></th></tr></thead><tbody><tr><td><strong>Token Factory</strong> <br><strong>(CT, DS, LVT)</strong></td><td><a href="https://eth.blockscout.com/address/0x96E0121D1cb39a46877aaE11DB85bc661f88D5fA?tab=contract"><code>0x96E0121D1cb39a46877aaE11DB85bc661f88D5fA</code></a></td><td><span data-option="Pl5VmPYwCQgM">PSM, </span><span data-option="mwlRALSuocDX">Vault, </span><span data-option="cClOkU0JUeqH">ERC20-Factory</span></td></tr><tr><td><strong>LP Token Implementation</strong><br><strong>(LPT)</strong></td><td><a href="https://eth.blockscout.com/address/0x083c322aDa898F880a1d0a959A6e69081B82E5bc?tab=contract"><code>0x083c322aDa898F880a1d0a959A6e69081B82E5bc</code></a></td><td><span data-option="yAvnMI9UjzJF">AMM, </span><span data-option="gST6gp8cSJx9">ERC20, </span><span data-option="k7PAV43g5UEQ">Implementation</span></td></tr></tbody></table>

### Misc. Contract Addresses (Ethereum)

<table><thead><tr><th width="140">Name</th><th width="383">Deployment Address</th><th>Labels<select multiple><option value="yAvnMI9UjzJF" label="AMM" color="blue"></option><option value="Z1pjjMq809JL" label="Entrypoint" color="blue"></option><option value="Pl5VmPYwCQgM" label="PSM" color="blue"></option><option value="mwlRALSuocDX" label="Vault" color="blue"></option><option value="bB4XMGpLBYa4" label="Factory" color="blue"></option><option value="QfNzTC8yj2Ak" label="Periphery" color="blue"></option><option value="5uzYDTLPOzBK" label="Singleton" color="blue"></option><option value="M9XKnEA5eqXG" label="ERC20" color="blue"></option><option value="4U9BZiW2ThN3" label="ERC20-Factory" color="blue"></option><option value="C1FDSWT4UgUK" label="External" color="blue"></option></select></th></tr></thead><tbody><tr><td><strong>PSM Exchange Rate Provider</strong></td><td><a href="https://eth.blockscout.com/address/0x7b285955DdcbAa597155968f9c4e901bb4c99263?tab=contract"><code>0x7b285955DdcbAa597155968f9c4e901bb4c99263</code></a></td><td><span data-option="Pl5VmPYwCQgM">PSM, </span><span data-option="QfNzTC8yj2Ak">Periphery</span></td></tr><tr><td><strong>Withdrawal Timelock</strong> <br><strong>(1.5-day)</strong></td><td><a href="https://etherscan.io/address/0xB4e8C9E712009A489940cbB372666bE3218BE7b8#readProxyContract"><code>0xB4e8C9E712009A489940cbB372666bE3218BE7b8</code></a></td><td><span data-option="mwlRALSuocDX">Vault, </span><span data-option="Pl5VmPYwCQgM">PSM, </span><span data-option="QfNzTC8yj2Ak">Periphery</span></td></tr><tr><td><strong>Legacy Withdrawal Timelock</strong> <br><strong>(Vault only)</strong></td><td><a href="https://eth.blockscout.com/address/0xf27e7e8A854211E030cfCd39350827CC15eFf721?tab=contract"><code>0xf27e7e8A854211E030cfCd39350827CC15eFf721</code></a></td><td><span data-option="mwlRALSuocDX">Vault, </span><span data-option="QfNzTC8yj2Ak">Periphery</span></td></tr></tbody></table>

### External Contract Addresses (Ethereum)

<table><thead><tr><th width="140">Name</th><th width="383">Deployment Address</th><th>Labels<select multiple><option value="yAvnMI9UjzJF" label="AMM" color="blue"></option><option value="Z1pjjMq809JL" label="Entrypoint" color="blue"></option><option value="Pl5VmPYwCQgM" label="PSM" color="blue"></option><option value="mwlRALSuocDX" label="Vault" color="blue"></option><option value="bB4XMGpLBYa4" label="Factory" color="blue"></option><option value="QfNzTC8yj2Ak" label="Periphery" color="blue"></option><option value="5uzYDTLPOzBK" label="Singleton" color="blue"></option><option value="M9XKnEA5eqXG" label="ERC20" color="blue"></option><option value="4U9BZiW2ThN3" label="ERC20-Factory" color="blue"></option><option value="C1FDSWT4UgUK" label="External" color="blue"></option></select></th></tr></thead><tbody><tr><td><strong>UniswapV4 Universal Router</strong></td><td><a href="https://etherscan.io/address/0x66a9893cc07d91d95644aedd05d03f95e1dba8af"><code>0x66a9893cc07d91d95644aedd05d03f95e1dba8af</code></a></td><td><span data-option="Z1pjjMq809JL">Entrypoint, </span><span data-option="yAvnMI9UjzJF">AMM, </span><span data-option="5uzYDTLPOzBK">Singleton, </span><span data-option="C1FDSWT4UgUK">External</span></td></tr><tr><td><strong>UniswapV4 Pool Manager</strong></td><td><a href="https://etherscan.io/address/0x000000000004444c5dc75cB358380D2e3dE08A90"><code>0x000000000004444c5dc75cB358380D2e3dE08A90</code></a></td><td><span data-option="Z1pjjMq809JL">Entrypoint, </span><span data-option="yAvnMI9UjzJF">AMM, </span><span data-option="5uzYDTLPOzBK">Singleton, </span><span data-option="C1FDSWT4UgUK">External</span></td></tr></tbody></table>
{% endtab %}
{% endtabs %}

