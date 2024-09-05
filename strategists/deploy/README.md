
# Overview of the deployment process

**The complexity of the deployment process varies depending on the circumstances. When deploying the first cellar, more initial setup is required. The strategist must deploy certain prerequisites that can be reused for subsequent cellars. If the necessary contracts are already deployed, they can proceed directly to the "Cellar" section. Otherwise, they need to start with the "Prerequisites."**

For a first-time deployment, the strategist will need to deploy and configure several components: the deployer, price router, registry, and adapters. Once these are in place, they can move on to deploying the cellar. After the cellar is deployed, ownership of the cellar contract must be transferred to either the Axelar proxy or Gravity.

Strategists need to deploy a set of instances of price router, registry and adaptors to every chain they want to use.

This guide will walk through the technical aspects of deploying a cellar, with references to relevant code examples.
