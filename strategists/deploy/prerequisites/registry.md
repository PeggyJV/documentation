# Registry

[**Registry**](../../../smart-contracts/advanced-smart-contracts/registry-details) keeps track of trusted adaptors and positions.
On initial deploy registry should be configured with adaptors and positions needed for the cellar. Adaptors and positions can also be added later through governance.

Setting up the registry, price router, adaptors and positions should be done in one deploy script which makes the process simpler. That way the references to different instances could be used right away. Because of that the examples for the next parts reference the same script.

Deploying registry and trusting adaptors and positions looks like this:

``` solidity
Registry public registry;
registry = Registry(deployer.deployContract(registryName, creationCode, constructorArgs, 0));
registry.setAddress(2, address(priceRouter));

registry.trustAdaptor(erc20Adaptor);
registry.trustPosition(ERC20_USDC_POSITION, address(erc20Adaptor), abi.encode(USDC));
```

Deploying the registry could be a part of [**a script**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/script/Arbitrum/peggyjv_production/SetupTestProductionInfra.s.sol) that deploys price router, registry and adaptors at once.

When the registry is deployed the ownership of it has to be transferred to the multisig.
