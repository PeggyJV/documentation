---
description: About Sommelier
---

# What is Sommelier

### Introduction and Evolution of Market

As the market gets flushed with new protocols, and products from the traditional finance world (derivatives, perps, leverage, etc.) enter the DeFi ecosystem, the era of simple funds and basic strategies needs a turbocharged remake. The majority of existing platforms in the current market perform actions that were best suited to the past few years. For example, some invest in an index portfolio. Others simply invest users' money into a specific pool, and then reinvest rewards over time. In this scenario, when the pool-specific vault stops working and the users abandon the vault like a deserted city, they move on to newer pool-specific vaults that work in the newer market conditions.

But, what if there was a way for us to create an intelligent vault that could adjust its composition based on current market conditions or predetermined metrics? Moreover, what if there was an architecture that allows the metric calculations to be done off-chain while utilizing live data feeds to adjust your current positions, and only the newer instruction set needed to be sent to the vault? We would be able to create more nimble, flexible, and most importantly, profitable and resilient vaults. Further, in traditional finance, we have come to rely on the power of off-chain computation using financial and data modeling techniques, because it allows your strategy to be proprietary and include the work of highly sophisticated quant and data science teams. Sommelier aims to do all of this but without the tradeoff of centralization that exists in traditional finance.

### Sommelier Intelligent Vaults

Welcome to the era of intelligent vaults that no longer become obsolete once the market changes. Sommelier vaults are intelligent meaning that they can predict, react, optimize and evolve to DeFi conditions.&#x20;

**Predict (example):** Use time-series forecasting methods to estimate future base yields.

**React (example):** Rebalance positions as information about major market moves (like a de-peg) becomes available. If market conditions change, vaults are able to rapidly adjust leverage ratios to help avoid liquidation.

**Optimize (example):** Use a sophisticated solution to accumulate leverage that is highly capital efficient and significantly reduces gas/flash loan fees that are otherwise typically incurred. Additionally, can optimize Uni V3 tick ranges while accounting for impermanent loss.

**Evolve (example):** Update its algorithm and integrated protocols to continually capture new yield opportunities.

We have deliberately rethought every layer of execution to be able to provide one of the first mid-frequency strategy execution platforms on a highly secure validator set (and not a multi-sig wallet).

### Architecture Overview

Sommelier is a decentralized asset management protocol, built on the Cosmos SDK, with a bridge to high-value EVM networks.&#x20;

<figure><img src=".gitbook/assets/Somm Deck.jpg" alt="" width="563"><figcaption></figcaption></figure>

The protocol is powered by the validator set that reaches consensus on all rebalance messages sent by strategists before they are executed on-chain. It should be noted that Strategists are given "whitelisted Strategist Functions" that allow them to rebalance specific vaults that they manage without reaching a consensus on every rebalance. Strategists must be approved via the consensus methods from the validator set of course first. \
\
By allowing rebalance calculations to be done off-chain, Sommelier employs a unique architecture to keep the strategies powering the vaults private and allows strategists to employ a wide variety of off-chain data modeling techniques used in traditional finance. Once consensus is reached on the rebalance message, it is passed through a bridge (either Sommelier's proprietary bridge to Ethereum or the Axelar bridge to alt-EVMs) where it then interacts with smart contracts in the native environment to execute the rebalance. Importantly, funds are not bridged.

The protocol also aggregates and batches transactions, thereby reducing gas fees and providing an attractive alternative to Ethereum’s high transaction costs. As for security, Sommelier uses a decentralized governance mechanism run by validators, ensuring state-of-the-art safety of user funds.

Sommelier as a protocol is non-custodial and entirely governed in a decentralized manner by its users.

### **Key Benefits**

* **Off-Chain Computation:** Unique architecture that allows Sommelier to ingest data input from off-chain models. This keeps the strategy private as well as allows the vault to be fed information from data models.
* **No Bridged Assets:** Multichain access without bridged assets.
* **Cosmos SDK for Efficiency:** By using the Cosmos SDK, Sommelier can offer a modular and robust protocol with Tendermint Consensus, a world-class and tested protocol layer. Using Cosmos allows fewer transactions to be performed on Ethereum, which keeps gas costs low.
* **Controlled by Validators and Decentralized Governance:** Transactions are managed by the Sommelier validator set with decentralized governance, so Sommelier cannot steal user funds, the network function is entirely responsive to user preferences and the protocol remains censorship-resistant.
