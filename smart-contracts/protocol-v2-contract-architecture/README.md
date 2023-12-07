---
description: >-
  This section of the Sommelier docs outlines the Smart Contract Architecture
  and its details. The docs are under continuous revision, so some sections are
  not published yet.
---

# Protocol (V2) Contract Architecture

**A list of useful links is provided below depending on what information you are seeking. For all paths, read this page, the next page first (**[**"Smart Contract Architecture Walk-Through"**](https://app.gitbook.com/o/4atpZ4Rl0cQ7Zflpk08d/s/gTlidpvGOM2ZKa8GDX86/\~/changes/9/smart-contracts/protocol-v2-contract-architecture/smart-contract-architecture-walk-through)**), and the** [**"Roles & Permissions"**](../roles-and-permissions.md) **page outlining different actors for Cellars.**

1. Developers looking to understand the architecture: read the rest of this page and the [next page.](https://app.gitbook.com/o/4atpZ4Rl0cQ7Zflpk08d/s/gTlidpvGOM2ZKa8GDX86/\~/changes/9/smart-contracts/protocol-v2-contract-architecture/smart-contract-architecture-walk-through-text)
2. Developers looking to deeply understand the core contracts: go to the ["Advanced Smart Contracts"](https://app.gitbook.com/o/4atpZ4Rl0cQ7Zflpk08d/s/gTlidpvGOM2ZKa8GDX86/\~/changes/9/smart-contracts/advanced-smart-contracts) section, and check out the detailed subpages and APIs (and some fun schematics). Also, feel free to check out the [deployed versions](../smart-contract-addresses.md) of this code after reading over this first page. Of course, all code can be found in the [github repository](https://github.com/PeggyJV/cellar-contracts/tree/main).
3. Developers looking to create an `Adaptor`: Skip to the "External Protocol Integration" section with ["Building Adaptors"](https://app.gitbook.com/o/4atpZ4Rl0cQ7Zflpk08d/s/gTlidpvGOM2ZKa8GDX86/\~/changes/9/smart-contracts/external-protocol-integration/building-adaptors) for a tutorial _(still in progress)._&#x20;
4. Developers looking to create a `PriceExtension`: Skip to the "External Protocol Integration" section with ["Building Price Extensions."](https://app.gitbook.com/o/4atpZ4Rl0cQ7Zflpk08d/s/gTlidpvGOM2ZKa8GDX86/\~/changes/9/smart-contracts/external-protocol-integration/building-pricing-extensions)
5. Developers only seeking actual, in-prod deployments: See ["Smart Contract Addresses."](../smart-contract-addresses.md)

Now, onto the core smart contracts architecture currently adopted by the Sommelier protocol.

## Core Architecture

<figure><img src="../../.gitbook/assets/Sommelier_Protocol_Smart_Contract_Architecture.jpg" alt=""><figcaption><p>High-Level Smart Contract Architecture</p></figcaption></figure>

The V2 Cellars infrastructure contains a number of smart contracts that contribute to the continued operation of each cellar and the Sommelier Cellars protocol.

The following four concepts categorize the scope of smart contract functionality in Cellars V2:

* **Registry** - contains global configuration applicable across Cellars, including references to other smart contracts and permissions layers for adaptors and Cellars.
* **PriceRouter** - global Cellars pricing contract, typically using Chainlink. Built to price complex multi-token Cellar positions (e.g. LP positions). Price Router upgrades now allow for alternative price sources other than Chainlink where deemed appropriate (see "Building Pricing Extensions").
* **Cellars -** user-facing ERC4626 vaults where strategists deploy assets to earn yield. Each Cellar is run by a single strategist, implementing various independent methods of earning yield. **Each Strategist can make adjustments to the respective Cellar they're managing, via Sommelier governance and Gravity Bridge-approved strategist functions.**
  * _**Note:** Sommelier Cellars depart from the ERC4626 standard in_ [_their implementation_](https://github.com/PeggyJV/cellar-contracts/blob/main/src/base/Cellar.sol#L922) _of `totalAssets`. According to the standard, `totalAssets` must not revert. For Sommelier Cellars, `totalAssets` may revert. Integrators should take care to note and handle this difference._
* **Adaptors -** integration contracts that allow Cellars to interact with an external DeFi protocol. Adaptors should be case-specific, and a Cellar may use multiple adaptors in the course of complex DeFi operations.

> There have been iterations on the above concepts throughout the lifetime of the Sommelier protocol. These are noted in each of their detailed subsections. A full V3 architecture upgrade is being currently discussed but not in the immediate development timeline.

Please see the next page for a general walk-through of the setup of a Cellar and its parts.
