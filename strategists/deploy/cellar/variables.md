# Variables
 
This guide is following the "test Real Yield USD" cellar deploy [**script**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/script/Arbitrum/peggyjv_production/DeployTestCellar.s.sol).

Before setting up the contract we need to gather variables. Price Router, Registry and Adaptors are already deployed and can be queried from the deployer:

``` bash
registry = Registry(deployer.getAddress(registryName));
priceRouter = PriceRouter(deployer.getAddress(priceRouterName));
erc20Adaptor = deployer.getAddress(erc20AdaptorName);
```
Note that the contract names should be constants saved somewhere, for example in [**a file like this**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/test/resources/Arbitrum/ArbitrumAddressesPeggyJV.sol).

In case of cellar permutation with oracle, the ERC4626SharePriceOracle should also be setup:
``` bash
ERC4626SharePriceOracle.ConstructorArgs memory args;
args._heartbeat = 1 days;
args._deviationTrigger = 0.005e4;
args._gracePeriod = 1 days / 3;
args._observationsToUse = 4;
args._automationRegistry = automationRegistry;
args._automationRegistrar = automationRegistrar;
args._automationAdmin = devStrategist;
args._link = address(LINK);
args._startingAnswer = 1e18;
args._allowedAnswerChangeLower = 0.75e4;
args._allowedAnswerChangeUpper = 1.25e4;
args._sequencerUptimeFeed = ARB_SEQUENCER_UPTIME_FEED;
args._sequencerGracePeriod = 3_600;
```
Explanations of the variables can be found in [**ERC4626SharePriceOracle contract**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/ERC4626SharePriceOracle.sol).
