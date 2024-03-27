# Sommelier Protocol Overview

Sommelier is a platform for running arbitrary strategies expressed through special smart contract vaults called Cellars. Sommelier can run Cellars on many different chains, and any user can deposit to a Cellar and provide capital for the strategy to utilize and share in the its performance. Cellars may accrue fees, flat or performance-based, for strategists and the Sommelier protocol. Fees earned by the Sommelier protocol are converted to SOMM tokens and distributed to Sommelier stakers.

The Sommelier protocol is architected to minimized trust and maintain decentralization at every step: 

- Strategists do not have direct control over Cellar contracts, but instead must express call data recommendations for the Sommelier chain to evaluate and sign itself
- Only a limited set of functions are exposed to strategists through the strategist API to minimize their ability to act maliciously 
- Users interact directly with the Cellar contracts on their respective chains to deposit and withdraw and thus do not have to expose their funds to a bridge 
- Sommelier validators have granular control over which strategists they are willing to accept call data from for each Cellar, creating a marketplace for strategies
- Relaying of contract calls from the Sommelier chain the the actual Cellar contract is permissionless 
- Governance, made up of SOMM stakers, controls the addition of new Cellars, which blockchains they can run on, authorization of strategists, and all other chain-specific aspects of the protocol.

## Components

To accomplish these features requires a somewhat complex architecture, the components of which are:

- *Cellar smart contracts*: The actual vault contracts existing on their respective blockchains
- *Bridges*: An in-house Gravity bridge fork and [Axelar General Message Passing](https://docs.axelar.dev/dev/general-message-passing/overview) are leveraged for relaying contract calls to Cellars
- *The Sommelier blockchain*: The Cosmos SDK chain at the heart of the system with the sole authority to originate contract calls to Cellars.
- *Steward*: A sidercar process run by validators that exposes the strategist API to strategists, a Cellar call authentication and authorization layer

Additionally strategists may coordinate with the Sommelier team to run peripheral smart contracts that interact with the frontend for things like Cellar share staking incentives. 

## Strategy Computation

Sommelier puts no restrictions on the systems use to make decisions about Cellar rebalancing recommendations. Strategists may use their own alpha by running arbitrary computations based on real-time market data to make the recommended Cellar calls via the strategist API.

This architecture enables vaults that are far superior to the typical static vault which rebalances deposits to the same allocation regardless of market conditions.

## More Information

- [Strategist Onboarding]()
- [Validator Onboarding]()

[other protocol outline links]()
