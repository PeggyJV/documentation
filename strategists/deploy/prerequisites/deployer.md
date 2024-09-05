# Deployer

[**Deployer**](https://github.com/PeggyJV/cellar-contracts/blob/main/src/Deployer.sol) enables strategists to deploy contracts to deterministic addresses and find them later by name. For that, a separate file should be created that includes contract names that will be used for the process. For example: [**ContractDeploymentNames.sol**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/resources/ContractDeploymentNames.sol)

dev0Address is needed for deploying the deployer and also for later deployments. As there will be more addresses coming they should be stored in one place. For example: [**ArbitrumAddressesPeggyJV.sol**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/test/resources/Arbitrum/ArbitrumAddressesPeggyJV.sol)

dev0Address should be wallet address that the strategist has access to.

Now we have everything we need to deploy the deployer. Next step would be running a deploy script. 

[**Example script for deploying a deployer**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/script/Arbitrum/peggyjv_production/DeployDeployer.s.sol)
