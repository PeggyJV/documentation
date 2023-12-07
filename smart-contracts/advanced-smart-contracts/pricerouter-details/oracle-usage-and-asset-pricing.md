# Oracle Usage and Asset Pricing

Sommelier Cellars are built on the notion of "real-time pricing". Given that adaptors can be built to interact with any external protocol in a variety of ways, the core interface guarantee that an adaptor must provide is that each adaptor:

* Must report a base pricing asset (see [`assetOf`](https://github.com/PeggyJV/cellar-contracts/blob/51a3f88a497d32f79290b803498128c0e8184b2b/src/modules/adaptors/BaseAdaptor.sol#L139C1-L140C1)).
* Must be able to report, for a given Cellar, its balance of that base asset (see [`balanceOf`](https://github.com/PeggyJV/cellar-contracts/blob/51a3f88a497d32f79290b803498128c0e8184b2b/src/modules/adaptors/BaseAdaptor.sol#L124)).
* **There must be a way to price the base asset in terms of the Cellar's base asset.**

This section will cover the third bullet point: how the Sommelier protocol efficiently prices position assets in terms of the Cellar's base asset. This pricing requirement allows a Cellar, at all times, to be able to report its TVL in calls to [`totalAssets`](https://github.com/PeggyJV/cellar-contracts/blob/51a3f88a497d32f79290b803498128c0e8184b2b/src/base/Cellar.sol#L1010) and `_accounting`. This is an essential aspect of the Cellar security model.\
\
When available, Sommelier Cellars use [Chainlink Data Feeds](https://data.chain.link/) for on-chain asset pricing. In the future, alternative oracles and additional redundancy mechanisms may be added.

#### Oracle Trust Assumptions

The Sommelier Cellars protocol currently requires a trust assumption that Chainlink Data Feeds will continue to be available and report accurate pricing. Malicious price reporting from Chainlink aggregators can lead to incorrect TVL valuations of Cellars, leading to under or over-assessments of fees. For this reason, Sommelier Governance must be extremely careful about supporting low-liquidity assets which may be subject to oracle manipulation attacks.&#x20;

In addition, non-operation of Chainlink aggregators can lead to stuck funds, since `totalAssets` checks (which use pricing) are required for all TVL-changing operations. If the pricing call for _any asset_ in a Cellar reverts, the entire `totalAssets` call will revert, effectively freezing the Cellar.\


### Pricing Sources

The [Price Router](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/price-router/PriceRouter.sol) serves as the entry point to all pricing operations on the Sommelier protocol. When an asset is added to the Price Router, the `_settings` argument will specify a `derivative` value: this value is used to determine the mechanism by which the asset may be priced.

Assets which can be priced directly are associated with the address for which the relevant Chainlink aggregator exists. At setup time, the Price Router will call the aggregator to test correct reporting and set up sanity bounds based on the `minAnswer` and `maxAnswer` reported by the aggregator.

For assets which may not be directly available via Chainlink Oracles, different `derivative` arguments can be used to inform how the Price Router should translate the reported asset into other assets that can be priced. For instance, aToken derivatives store a mapping between the aToken and the correct underlying token (e.g. `aWETH -> WETH`). Curve LP tokens use pricing information reported by the Curve pool itself.\
\
Additional mechanisms in the Price Router are in place to ensure that prices are continually up-to-date and don't deviate from expected values. See the Github repo for more information on expected deviations, caching, and automation for updating price feeds.&#x20;

### Balance Reporting

For adaptors that directly hold tokens that can be given a market price (for instance, an adapter for holding WETH outright), the adaptor's `balanceOf` call should simply report that Cellar's balance (see [ERC20Adaptor](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/ERC20Adaptor.sol#L75)). For tokens which represent compositions or derivatives of tokens with a market price, the adaptor's `balanceOf` call must translate the balance of the held asset into an equivalent balance in the Cellar's base asset.

The mechanism of this translation depends on the nature of the underlying position. For example, adaptors that hold LP tokens should contain logic for splitting the LP token into component parts and translating one token's balance into the base token's balance. See [UniswapV3Adaptor](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/UniSwap/UniswapV3Adaptor.sol#L117) for an example, where an LP position exists for a pool of two tokens, `token0` and `token1`. If the adaptor's base asset is in terms of `token0`, first, the adaptor must determine the amount of tokens of both `token0` and `token1` that can be redeemed for a given LP position, the amount of `token1` is converted into the equivalent amount of `token0`, then both amounts are summed together to report the entire balance.\


### ETH -> USD Conversion

For some assets which are priced via Chainlink data feeds, only ETH-based pools are supported (for instance, [CRV-ETH](https://data.chain.link/ethereum/mainnet/crypto-eth/crv-eth)). Since many Sommelier Cellars use USD-pegged coins as their underlying asset, the Price Router also requires that all assets can be priced in USD. In these cases, an additional pricing call is made to the ETH-USD feed, and a second price conversion is made to put the target asset in USD prices.

