# How to Deploy a Cellar Contract

Follow the instructions below to deploy a **[CellarWithMultiAssetDeposit](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/script/Arbitrum/test/DeployTestMultiAssetDeposit.s.sol)** contract using the provided script. In this example, we use **CellarWithMultiAssetDeposit** to demonstrate how to deploy a cellar contract.

## Step-by-Step Instructions

### 1. Prepare your Environment

Ensure you have the following environment variables configured:

- `ARBITRUM_RPC_URL` – Your Arbitrum node's RPC URL.
- `PRIVATE_KEY` – Your private key to authorize the deployment.
- `ARBISCAN_KEY` – Your Etherscan API key for verifying contracts on Arbitrum.

### 2. Understanding the Script

The deployment script (`DeployTestMultiAssetDepositScript`) performs the following key actions:

- **Deploys the Cellar Contract**:  
  The function `_createCellar` creates the **CellarWithMultiAssetDeposit** contract with the given parameters like name, symbol, holding asset, position, initial deposit, and platform cut.

- **Add Adapters and Positions**:  
  It adds necessary adaptors to the catalogue (e.g., `aaveV3ATokenAdaptor`) and positions (e.g., USDC, USDT).

- **Set Alternative Assets**:  
  The script configures alternative assets with specific fees for assets like USDC, DAI, and USDT.

- **Transfers Ownership**:  
  Once the cellar is configured, the ownership is transferred to the strategist (`devStrategist`).

### 3. Script Breakdown

- `vm.startBroadcast()` starts broadcasting the transactions.
- `CellarWithMultiAssetDeposit cellar = _createCellar(...)` deploys the new cellar contract.
- `cellar.addAdaptorToCatalogue(...)` adds necessary adapters for the positions.
- `cellar.addPosition(...)` adds the positions to the contract, allowing it to manage multiple assets.
- `cellar.setAlternativeAssetData(...)` sets up alternative assets with their respective fees.
- `cellar.transferOwnership(devStrategist)` transfers ownership to the desired strategist address.

### 4. Share Price Oracle Setup

If your cellar permutation requires an **ERC4626 Share Price Oracle**, ensure the oracle is set up to handle share pricing during user entry and exit. The setup involves configuring the share price oracle and ensuring it can calculate the cellar's total assets. 

- You will need to update the cellar to reference the **ERC4626 Share Price Oracle**.
- Make sure to handle the `SharePriceOracleUpdated` event and verify that the oracle has the correct decimals (usually 18).
- Full example of how to integrate the share price oracle is available [here](https://github.com/PeggyJV/cellar-contracts/blob/main/src/base/permutations/CellarWithOracle.sol#L58-L69).


### 5. Deploying

Use the following command to run the deployment script for **CellarWithMultiAssetDeposit**:

```bash
source .env && forge script script/Arbitrum/test/DeployTestMultiAssetDeposit.s.sol:DeployTestMultiAssetDepositScript --rpc-url $ARBITRUM_RPC_URL --private-key $PRIVATE_KEY --optimize --optimizer-runs 200 --with-gas-price 100000000 --verify --etherscan-api-key $ARBISCAN_KEY --slow --broadcast

You can modify the `--with-gas-price` value to reflect current network conditions.

#### Example Deployment Scripts

You can reference these deployment scripts as examples for how to deploy **Cellar** contracts in different environments:

- **Production Deployment Scripts**:  
  [Arbitrum Production Scripts](https://github.com/PeggyJV/cellar-contracts/tree/zaki/PeggyJYDeployments/script/Arbitrum/production)

- **Test Deployment Scripts**:  
  [Arbitrum Test Scripts](https://github.com/PeggyJV/cellar-contracts/tree/zaki/PeggyJYDeployments/script/Arbitrum/test)

Each of these scripts follows a similar structure to deploy **Cellar** contracts, configure positions, and add necessary adapters.

