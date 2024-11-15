# Flash Loans

There are two permutations for the flash loan functionality.
[CellarWithAaveFlashLoans](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/CellarWithAaveFlashLoans.sol) 
and 
[CellarWithBalancerFlashLoans](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/CellarWithBalancerFlashLoans.sol).

Deployment of flash loans cellar doesn't differ much from the simple deployment. 
Only extra step is to add the pools address as the constructor argument of the cellar.

In the **[Example Deployment script](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/script/Arbitrum/peggyjv_production/ExampleDeploy/DeployExampleCellar.s.sol)**
the change has to be made in the `_createCellar` helper function.

``` solidity
function _createCellar(
        string memory deploymentName,
        string memory cellarName,
        string memory cellarSymbol,
        ERC20 holdingAsset,
        uint32 holdingPosition,
        bytes memory holdingPositionConfig,
        uint256 initialDeposit,
        uint64 platformCut
    ) internal returns (CellarWithBalancerFlashLoans) {
        // Approve new cellar to spend assets.
        address cellarAddress = deployer.getAddress(deploymentName);
        holdingAsset.safeApprove(cellarAddress, initialDeposit);

        bytes memory creationCode;
        bytes memory constructorArgs;
        creationCode = type(CellarWithBalancerFlashLoans).creationCode;
        constructorArgs = abi.encode(
            cellarOwner,
            registry,
            holdingAsset,
            cellarName,
            cellarSymbol,
            holdingPosition,
            holdingPositionConfig,
            initialDeposit,
            platformCut,
            type(uint192).max,
            address(vault) -- ADDED THIS LINE
        );

        return
            CellarWithBalancerFlashLoans(
                deployer.deployContract(deploymentName, creationCode, constructorArgs, 0)
            );
    }
```
The address that we added comes from the [ArbitrumAddresses.sol](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/test/resources/Arbitrum/ArbitrumAddressesPeggyJV.sol).
Make sure it points to the correct Balancer or Aave vault. Also remember to change the permutation contract to the correct one, for example: `CellarWithOracle -> CellarWithBalancerFlashLoans`.
