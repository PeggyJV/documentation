# Price Router

[**Price Router**](https://sommelier-finance.gitbook.io/sommelier-documentation/smart-contracts/advanced-smart-contracts/pricerouter-details) contract provides reliable pricing of any assets and positions held by the strategist.

There are two options for the price router. PriceRouter.sol and the SequencerPriceRouter.sol which inherits from the first. [**PriceRouter.sol**](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/price-router/PriceRouter.sol) should be used for the mainnet and [**SequencerPriceRouter.sol**](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/price-router/permutations/SequencerPriceRouter.sol) for L2 chains.

Deploying SequencerPriceRouter:

``` solidity
PriceRouter public priceRouter;
creationCode = type(SequencerPriceRouter).creationCode;
constructorArgs = abi.encode(ARB_SEQUENCER_UPTIME_FEED, uint256(3_600), dev0Address, registry, WETH);
priceRouter = PriceRouter(deployer.deployContract(priceRouterName, creationCode, constructorArgs, 0));
```

Price router has to be configured with assets and price feeds. Sommelier Cellars mainly use Chainlink Data Feeds for pricing. Data feed addresses can also be imported from the [**address file**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/test/resources/Arbitrum/ArbitrumAddressesPeggyJV.sol).

``` solidity
uint256 price = uint256(IChainlinkAggregator(WETH_USD_FEED).latestAnswer());
settings = PriceRouter.AssetSettings(CHAINLINK_DERIVATIVE, WETH_USD_FEED);
priceRouter.addAsset(WETH, settings, abi.encode(stor), price);
```
[**Example of the full setup script**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/script/Arbitrum/peggyjv_production/SetupTestProductionInfra.s.sol)

