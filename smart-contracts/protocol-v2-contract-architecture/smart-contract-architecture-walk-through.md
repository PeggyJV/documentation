---
description: >-
  Onboarding page outlining key smart contract architecture oriented for people
  who prefer text-based learning
---

# Smart Contract Architecture Walk-Through

### High-Level Architecture Walk-Through

A key concept to understand is that the Sommelier smart contract architecture revolves around ERC4626 vaults. Users engage with the protocol through these vaults, by either depositing a base asset for a share token, or redeeming a share token for said base asset.

A good way to think about it is to imagine Sommelier-specific code pairing with ERC4626 vaults. This is a powerful dynamic because it allows other IERC4626 abiding vaults to be truly leveraged as the standard intended to enable. **The image shows the `Cellar.sol` with general transaction flow going from the Strategist to the `PermissionLayer` and the `IntegrationLayer`.**&#x20;

<figure><img src="../../.gitbook/assets/Sommelier_Protocol_Smart_Contract_Architecture.jpg" alt=""><figcaption><p>Schematic Highlighting Permission and Integration Layer with Cellar</p></figcaption></figure>

An important question that must be considered is: how does the protocol keep track of what vaults are acceptable, and what is being done with said funds within the vaults (AKA vault positions) within them?  That is where the `Registry` and `PriceRouter` come into play. They work together to trust adaptors, specific assets, and provide pricing methods for the total assets within a cellar, respectively.

The Registry, like all aspects of the smart contract architecture and implementation, ultimately, was brought into the Sommelier protocol through its governance process involving SOMM validators, SOMM holders, delegators, etc. See more on the protocol governance process [here](../../introduction/governance-process-overview.md).

<figure><img src="../../.gitbook/assets/Registry-Focused High-Level Schematic.jpg" alt=""><figcaption><p>Walk-Through Schematic Highlighting Trust Requirements for Cellars</p></figcaption></figure>

Through governance, the registry trusts specific adaptor code, and subsequently positions. It should be noted that a MultiSig is used to manage the Registry and PriceRouter smart contracts. This MultiSig is approved to be part of the Sommelier Smart Contract Architecture via voting and governance methods with the SOMM Validator Set as usual. Remember, Sommelier is governed ultimately by the Sommelier blockchain. The Sommelier governance can vote to kick out the MultiSig for another entity at any time, of course following governance procedures.

### MultiSig Operations

The MultiSig mainly takes care of the following aspects, which are detailed further in this section:

* Add `Adaptors` and `AdaptorPositions` to the `Registry.sol`
* Add `Assets` and their `Pricing` mechanisms (`extension smart contracts`, `pricefeeds`, etc.) to the `PriceRouter.sol` respective Sommelier Cellars will query to price their assets.

Positions are encoded details outlining adaptors with specific configurations (ERC20s involved, etc.). Position underlying assets must be priceable using the PriceRouter. Once adaptors and positions are trusted by the Registry, they must be trusted in each Cellar before the Cellar can use that position/adaptor., **as long as the pricing of the adaptor position assets is approved by the PriceRouter too.**&#x20;

The PriceRouter must have the function `addAsset()` called to add an asset and its `settings` that include its pricing source / logic. The `settings` could point to a price feed or even a contract that calculates `TWAP`, or other complicated pricing mathematic methods. The `PriceRouter` normalizes the positions within a `Cellar` to USD. This is important to keep accounting of the Cellar positions and thus the assets from users and protocols.

### SOMM Governance

The SOMM Governance manages what Cellar Adaptors and AdaptorPositions are added to respective Cellars. This can only be done once the approved MultiSig prepares the Registry and PriceRouter, with Adaptor, and Asset details, respectively. The SOMM Governance requires that these preparations have been made via the MultiSig, or else any attempts to add Adaptor positions will `revert`. This permission setup acts as a protective layer for the Sommelier Cellars, and the protocol as a whole, from malicious SOMM Governance voting in malicious external smart contracts to have the Cellars interact with. The Cellars are only able to have positions, aka external integrations, with adaptor positions that the MultiSig votes on as well.

Cellars can now have adaptors and positions added, and added to their `catalogue`. Each cellar has a `catalogue` that consist of specific strategist calls that do not require SOMM governance approval every time they are called. The addition and removal of positions and adaptors to  the`catalogue` are done via SOMM governance of course to start.&#x20;

For more details on these base smart contracts and APIs, check out ["Advanced Smart Contracts"](https://app.gitbook.com/o/4atpZ4Rl0cQ7Zflpk08d/s/gTlidpvGOM2ZKa8GDX86/\~/changes/9/smart-contracts/advanced-smart-contracts) (more specifically the first four pages on the core contracts mentioned so far). They reflect the most current versions of the respective smart contracts used within the Sommelier protocol.&#x20;
