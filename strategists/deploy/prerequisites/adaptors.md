# Adaptors

[**Adaptors**](../../../smart-contracts/advanced-smart-contracts/adaptors) integrate Cellars with external DeFi protocols, enabling them to use the assets in the protocol. Catalogue of adaptors is located in “src/modules/adaptors” and explanations about different adaptors can be found [**here**](https://github.com/PeggyJV/cellar-contracts/blob/main/docs/adapters.md).

Desired adaptors should be imported from the catalogue and be used like this:

``` solidity
address public aaveV3ATokenAdaptor;
creationCode = type(AaveV3ATokenAdaptor).creationCode;
constructorArgs = abi.encode(aaveV3Pool, aaveV3Oracle, AAVE_V3_MIN_HEALTH_FACTOR);
aaveV3ATokenAdaptor = deployer.deployContract(aaveV3ATokenAdaptorName, creationCode, constructorArgs, 0);
```
Later on the same aaveV3ATokenAdaptor variable could be used to trust the adaptor and positions on Registry:

``` solidity
registry.trustAdaptor(aaveV3ATokenAdaptor);
registry.trustPosition(AAVE_V3_LOW_HF_A_USDC_POSITION, address(aaveV3ATokenAdaptor), abi.encode(aV3USDC));
```

[**Example of the full setup script**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/script/Arbitrum/peggyjv_production/SetupTestProductionInfra.s.sol)

