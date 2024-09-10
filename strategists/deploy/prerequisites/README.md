# Overview of Prerequisites

Before deploying a Cellar contract, strategists need to ensure that several key components and configurations are set up. This section outlines the necessary prerequisites, including adaptors, the deployer contract, environment setup, the price router, and the registry. Each of these elements plays a crucial role in the smooth operation of a Cellar contract.

In the following sections, we will cover the core prerequisites for deploying a Cellar contract:

- **[Adaptors](strategists/deploy/prerequisites/adaptors.html)**: Learn about the various adaptors available in the cellar contracts, which integrate Cellars with external DeFi protocols. The adaptor catalogue is located in the `src/modules/adaptors` directory, and details about each adaptor can be found [**here**](https://github.com/PeggyJV/cellar-contracts/blob/main/docs/adapters.md).

- **[Deployer](strategists/deploy/prerequisites/deployer.html)**: The deployer enables strategists to deploy contracts to deterministic addresses and locate them by name. This section covers how to set up the deployer contract, examples for deploying the deployer, and how to use it for managing cellar deployments.

- **[Environment Setup](strategists/deploy/prerequisites/env-setup.html)**: Strategists must configure their development environment, including setting up Foundry for compiling and deploying contracts. The section also explains how to fill in the necessary environment variables and prepare for running deployment scripts.

- **[Price Router](strategists/deploy/prerequisites/price-router.html)**: The price router provides accurate pricing of assets and positions. This section details the deployment and configuration of the price router, including the SequencerPriceRouter for L2 chains, as well as how to configure assets and price feeds.

- **[Registry](strategists/deploy/prerequisites/registry.html)**: The registry keeps track of trusted adaptors and positions. It must be set up initially to trust the adaptors and positions used by the cellar. This section covers the deployment and configuration of the registry, along with examples of integrating it with the price router and adaptors.

By ensuring these prerequisites are in place, strategists can smoothly deploy their cellar contracts, leveraging the full functionality of the system.
