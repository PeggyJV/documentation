---
description: A detailed breakdown of the PriceRouter.sol contract.
---

# PriceRouter Details

Cellars rely on efficient and reliable pricing of any assets and positions held by the strategist. Pricing is used not only to determine Cellar performance and earned fees for the protocol and strategist, but it is also a critical part of the Cellar security model: pricing is used to make sure that strategist rebalances do not change the Cellar's TVL outside a defined envelope, preventing both mistakes and malicious operations by strategists.

Sommelier Cellars mainly use [Chainlink Data Feeds](https://chain.link/data-feeds) for pricing. The [PriceRouter contract](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/price-router/PriceRouter.sol) provides a pricing frontend to all other components of the Sommelier Cellars protocol. Each position that a Cellar takes must have a supported pricing mechanism: the Registry [validates this](https://github.com/PeggyJV/cellar-contracts/blob/c8d427c013013ba4fd7e8ea7fd25dbdea3d65efd/src/Registry.sol#LL446C26-L446C26) during a `trustPosition` call by checking `PriceRouter#isSupported`. Given the universal nature of the Price Router (used by all Cellars), the Price Router is owned by governance.

The principal governance workflow for the Price Router is the calling of `addAsset` , in order to set up pricing for new adaptors and positions. Each Adaptor specifies an underlying asset (see [`BaseAdaptor#assetOf`](https://github.com/PeggyJV/cellar-contracts/blob/51a3f88a497d32f79290b803498128c0e8184b2b/src/modules/adaptors/BaseAdaptor.sol#L139)), and before any adaptor is trusted by the registry, pricing for the underlying asset must be registered by calling `addAsset`. When adding an asset, different token types follow different pricing logic defined by `_settings.derivative.` For instance, for LP tokens, pricing is set up such that each token is broken down into its component ERC20 parts, and the prices of each token are fetched separately and summed to create a total pricing for the LP token.

> Current `PriceRouterV2` contract accepts `settings.derivative` equal to 1, 2, or 3. The first two options correspond to a `ChainlinkPriceFeed` and `TWAP`, respectively. The third option points to a contract following the `Extension` contract structure where custom pricing methods can be created. See ["Building Pricing Extensions"](../../external-protocol-integration/building-pricing-extensions.md) section for more information.

<figure><img src="../../../.gitbook/assets/PriceRouter Specifics.jpg" alt=""><figcaption><p>PriceRouter Schematic</p></figcaption></figure>

In addition to providing logical instructions on how to price the asset, the `addAsset` call requires the caller to provide an `_expectedAnswer`, which will be checked against the price calculated on-chain. Assets whose reported on-chain pricing does not match the expected answer will fail when being added. This may mean that the pricing logic is incorrect and needs to be changed, the settings parameters are incorrect, or the expected answer was calculated incorrectly.

Once an asset is added via `addAsset`, any of the `view` functions that represent pricing operations can be called:

* `getPriceInUSD`
* `getValue`
* `getValuesDelta`
* `getValues`
* `getExchangeRate`
* `getExchangeRates`

### Gas Savings, Automation, and the `SharePriceOracle.sol`

CellarV2.5 architecture uses Chainlink automation with an \`ERC4626SharePriceOracle\` smart contract that stores prices from `performUpkeep` performed for a respective Cellar. The respective Cellar will use the ERC4626SharePriceOracle for pricing by default. It carries out comparisons between the latest price and the TWAP.

> For V2.5 Cellars using share price oracles, if the oracle is deemed `notSafeToUse`, the TXs will `REVERT`. This is to avoid the share price having massive movements because the real share price has diverged from the oracle value (because the oracle has not been running, and thus is not safe to use).

<figure><img src="../../../.gitbook/assets/SharePriceOracle Schematic.jpg" alt=""><figcaption><p>ERC4626 Share Price Oracle Schematic</p></figcaption></figure>

See the next page for a current API for `PriceRouter.sol`.
