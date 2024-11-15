# Native support 

In order to deploy cellar with native support, the Cellars constructor should be called inside `payable`.

For `CellarWithOracleWithBalancerFlashLoansWithMultiAssetDepositWithNativeSupport` it would look like this:
``` solidity
function _createCellarWithNativeSupport(
        string memory deploymentName,
        string memory cellarName,
        string memory cellarSymbol,
        ERC20 holdingAsset,
        uint32 holdingPosition,
        bytes memory holdingPositionConfig,
        uint256 initialDeposit,
        uint64 platformCut
    ) internal returns (CellarWithOracleWithBalancerFlashLoansWithMultiAssetDepositWithNativeSupport) {
        // Approve new cellar to spend assets.
        string memory nameToUse = deploymentName;
        address cellarAddress = deployer.getAddress(nameToUse);
        holdingAsset.safeApprove(cellarAddress, initialDeposit);

        bytes memory creationCode;
        bytes memory constructorArgs;
        creationCode = type(CellarWithOracleWithBalancerFlashLoansWithMultiAssetDepositWithNativeSupport).creationCode;
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
            address(vault)
        );

        return
            CellarWithOracleWithBalancerFlashLoansWithMultiAssetDepositWithNativeSupport(
                payable(deployer.deployContract(nameToUse, creationCode, constructorArgs, 0))
            );
    }
``` 
