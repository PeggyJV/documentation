# Permutations

The core functionality of a Cellar contract is implemented in [**Cellar.sol**](https://github.com/PeggyJV/cellar-contracts/blob/main/src/base/Cellar.sol). However, strategists typically deploy specialized versions, or "permutations," of this base contract. These permutations are designed to meet various strategic requirements and can be found in [**src/base/permutations**](https://github.com/PeggyJV/cellar-contracts/tree/main/src/base/permutations).

## Available Building Blocks

Strategists can leverage the following modules when selecting a permutation to deploy:

- **AaveFlashLoans**: Enables the use of Aave flash loans within the Cellar.
- **BalancerFlashLoans**: Allows the use of Balancer flash loans.
- **MultiAssetDeposit**: Supports deposits using multiple asset types.
- **NativeSupport**: Adds support for native assets (e.g., ETH).
- **Oracle**: Integrates price oracle functionality.
- **ShareLockPeriod**: Imposes a lock period on share redemptions.

These building blocks are combined in different permutations to offer tailored strategies based on the desired functionality.

## Permutation Examples

Below are some of the key contract permutations available for deployment. Each permutation combines specific building blocks to extend the base functionality:

- [**CellarWithOracleWithAaveFlashLoansWithMultiAssetDeposit**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/advanced/CellarWithOracleWithAaveFlashLoansWithMultiAssetDeposit.sol)  
  Combines Oracle support, Aave flash loans, and multi-asset deposit capabilities, allowing strategists to take advantage of flexible asset management and price data integration.

- [**CellarWithOracleWithAaveFlashLoansWithMultiAssetDepositWithNativeSupport**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/advanced/CellarWithOracleWithAaveFlashLoansWithMultiAssetDepositWithNativeSupport.sol)  
  Extends the above permutation with native asset support, enabling seamless interaction with assets like ETH.

- [**CellarWithOracleWithBalancerFlashLoansWithMultiAssetDeposit**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/advanced/CellarWithOracleWithBalancerFlashLoansWithMultiAssetDeposit.sol)  
  Similar to the Aave permutation but utilizing Balancer flash loans instead, providing strategists with a different liquidity source for complex financial operations.

- [**CellarWithOracleWithBalancerFlashLoansWithMultiAssetDepositWithNativeSupport**](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/advanced/CellarWithOracleWithBalancerFlashLoansWithMultiAssetDepositWithNativeSupport.sol)  
  Extends the Balancer permutation with native asset support, allowing interaction with assets like ETH.


## How to Select a Permutation

To determine which permutation to deploy, strategists should evaluate the specific requirements of their strategy. For example:

- **Flash loan capabilities**: Choose between [Aave](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/advanced/CellarWithOracleWithAaveFlashLoansWithMultiAssetDeposit.sol) or [Balancer](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/advanced/CellarWithOracleWithBalancerFlashLoansWithMultiAssetDeposit.sol) based on your preferred liquidity provider.

- **Multi-asset support**: Use permutations with [`MultiAssetDeposit`](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/advanced/CellarWithOracleWithAaveFlashLoansWithMultiAssetDeposit.sol) if you need to handle multiple types of tokens.

- **Native asset support**: If your strategy interacts with native assets like ETH, ensure you choose a permutation with [`NativeSupport`](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/advanced/CellarWithOracleWithAaveFlashLoansWithMultiAssetDepositWithNativeSupport.sol).

- **CellarWithOracleWithBalancerFlashLoansWithMultiAssetDepositWithNativeSupport**:  
  This permutation extends the Balancer flash loan functionality by combining Oracle support, multi-asset deposits, and native asset (e.g., ETH) support. You can find the contract [here](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/permutations/advanced/CellarWithOracleWithBalancerFlashLoansWithMultiAssetDepositWithNativeSupport.sol).

The full list of available permutations and building blocks can be explored in the [`src/base/permutations` directory](https://github.com/PeggyJV/cellar-contracts/tree/zaki/PeggyJYDeployments/src/base/permutations). Each file is structured to make the functionality of each permutation clear, and comments are provided to guide you in understanding how the different modules interact.

## Deploying with permutations 

Some features that the permutations provide, need additional configuration when deploying the cellar. 
Rather than repeating the same deploying script with minor changes, this doc just brings out the additional steps needed to deploy with different permutations.
Use the **[Example Deployment script](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/script/Arbitrum/peggyjv_production/ExampleDeploy/DeployExampleCellar.s.sol)**
as the base and add additional permutation logic from the following docs.
