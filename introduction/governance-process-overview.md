---
description: The Sommelier protocol is governed by SOMM token holders.
---

# Governance Process Overview

## Governance Overview

The Sommelier protocol is governed by SOMM token holders, utilizing Cosmos governance modules supporting Cosmos SDK proposal types as of v0.45.\
\
A number of items must be voted on and approved by the community including  strategist onboarding, Vault (Cellar) activations, community spend proposals (which allocate funding from the SOMM community pool), protocol upgrades, and parameter changes.&#x20;

## **Community Approval Process**&#x20;

* [Proposals](https://www.mintscan.io/sommelier/proposals) must be posted to the [Forum](https://community.sommelier.finance/c/governance/4) for a minimum of three days, after which point they can be submitted as on-chain votes via [Keplr](https://wallet.keplr.app/chains/sommelier?tab=governance). This process is referred to as the SIPs process and is outlined [here](https://community.sommelier.finance/t/sommelier-improvement-proposal-guide-sips/29).
* Proposers should be available to answer any questions from the community during the feedback period on the forum.&#x20;
* Proposers may want to consider hosting a community AMA, especially if you are a new strategist.
* After the forum period, proposers should incorporate any pertinent community feedback and move the proposal to an on-chain vote on [Keplr](https://wallet.keplr.app/chains/sommelier?tab=governance). On-chain votes last for 48 hours.&#x20;
* To discourage proposal spam, a deposit of 5000 SOMM must be made when submitting a proposal. If a full deposit is sent with proposal submission, it will immediately enter the voting period. Otherwise, the deposit period will last up to a maximum of 48 hours, during which period other token holders may contribute to the deposit to reach the required amount. If the deposit is not met within the 48 hours, the proposal will not move to an on-chain vote.&#x20;
* In order to pass, a governance proposals must reach a quorum of 50% staked SOMM and an approval threshold of 50% staked SOMM. As with most standard Cosmos SDK chains, validators can vote with delegated voting power. Stakers can also vote directly and have the option to override their delegate (validator.) Due to the short voting period and high quorum requirement, Sommelier relies on the active involvement and consent of validators and stakers.
* To determine when your proposal will need to be posted to the forum, start with the date you want your proposal to go live and work backwards, counting only business days.&#x20;
  * T0: Go live date
  * T-2: On-Chain vote
  * T-5: Forum post
* It will be harder to reach quorum over the weekend, so it is not recommended to start an on-chain vote on a Friday or Saturday.&#x20;
* If a majority of non-abstaining voting power votes "Yes," a governance proposal will pass, unless greater than 1/3 of the dissenting power has chosen to vote "No With Veto."  &#x20;

## Proposal Types

Sommelier governance consists of the standard Cosmos SDK proposal types, with additional protocol-specific proposals. All proposal types have the same quorum and approval threshold requirements. &#x20;

## Types of Proposals

### **Text Proposals**&#x20;

Text proposals describe a desired action for the protocol to take, but do not execute any code when voting completes. These typically include general policies or philosophies of how the chain should operate or signaling proposals to get community consent for an upcoming feature or decision.

#### [Strategist Onboarding ](#user-content-fn-1)[^1]

* New strategists will need to create a forum post outlining relevant background experience and why the Sommelier community should approve you to be a strategist. Please provide as much information as possible about your team as strategist reputation and track-record is one of the factors most heavily evaluated during the governance process. Please include links to your online presence as well.
* Please consider using the examples below as a template:
  * [example ](#user-content-fn-2)[^2]
* If the proposal passes and governance has approved you as a strategist, you are now able to operate Cellars through Steward. Once you are approved / voted in as a Strategist, you can use whitelisted strategist functions and send encoded messages using Steward to manage said Cellars without SOMM validator set governance approval for every rebalance.

### On-Chain Proposals&#x20;

#### Vault Activation Proposals

* Strategists that would like to launch a vault on Sommelier will need to receive community approval.&#x20;
* The proposal should clearly explain the strategy to be deployed and the qualifications of the strategist. &#x20;
* Please consider using the examples below as a template:
  * [Forum example](https://community.sommelier.finance/t/sips-014-015-upcoming-cleargate-eth-btc-cellar-proposals/969)
  * [On-chain version](https://wallet.keplr.app/chains/sommelier/proposals/14)
* These proposals are on-chain, meaning any on-chain instructions included in the proposal will be executed upon conclusion of the vote. In this case, there are a set of custom proposals for the addition or removal of cellar IDs defined in the gravity module. Cellar IDs are addresses of Ethereum smart contracts designated as cellars. These target addresses will be callable by strategists to manage the cellar once approved by governance.&#x20;

### Community Spend Proposals&#x20;

The community pool holds a large allocation of SOMM to fund governance-driven efforts. These proposal types describe the intended use, a destination address, and an amount, which is transferred upon vote completion.&#x20;

#### Liquidity Mining Program&#x20;

* Strategy Providers that would like to add liquidity mining rewards to their cellar, will need to receive approval to provide these rewards from the community pool.
* The duration of the liquidity mining program should be specified in the proposal. Any extension of the program will require another governance proposal.
* Please consider using the examples below as a template:
  * [Forum example](https://community.sommelier.finance/t/sips-019-020-upcoming-eth-btc-momentum-eth-btc-trend-liquidity-mining-incentives-proposals/1013)
  * [On-chain version](https://wallet.keplr.app/chains/sommelier/proposals/21)
* These proposals are on-chain, meaning any on-chain instructions included in the proposal will be executed upon conclusion of the vote. Sommelier's gravity module has defined a proposal for Ethereum spends, in which the destination address can be on Ethereum meaning funds can be bridged to an Ethereum destination address upon passing.

#### Grant for the Creation of Multiple Strategies&#x20;

* Strategists that would like to receive a grant to create multiple strategies over an extended period of time will need to submit a proposal for a grant from the community pool.
* The grant size, scope of work, and expected period of deployment should be specified in the proposal.
* It is recommended to provide as much detail as possible for all grant terms as ambiguity creates opportunities for governance disputes as to the effective deployment of a grant.&#x20;
* Please consider using the examples below as a template:
  * [Forum example](https://community.sommelier.finance/t/sips-013-cleargate-upcoming-community-spend-proposal/956)
  * [On-chain version](https://wallet.keplr.app/chains/sommelier/proposals/13)
* These proposals are on-chain, meaning any on-chain instructions included in the proposal will be executed upon conclusion of the vote. Sommelier's gravity module has defined a proposal for Ethereum spends, in which the destination address can be on Ethereum and funds can be bridged to an Ethereum destination address upon passing.&#x20;

#### KPI Incentives Proposal&#x20;

* Strategists may submit a community spend proposal to receive incentives if an active Cellar achieves pre-defined KPIs using this [template](https://community.sommelier.finance/t/discussion-about-establishment-of-a-kpi-incentives-council-kpic/1082/2).&#x20;
* Strategists may submit KPI-incentive proposals at any time, but rewards will always be distributed at the end of the quarter.
* A thorough analysis of the accomplishment of pre-defined KPIs must be posted by Strategists in the forum within 24 hours of the last day of the quarter at 0:00 GMT. The first time a Cellar receives KPI incentives, performance should be assessed to date, from the time of Cellar launch through the most recent quarter.
* Please see the full process, outlined [here](https://community.sommelier.finance/t/discussion-about-establishment-of-a-kpi-incentives-council-kpic/1082/2), for more information on calculating KPI metrics.&#x20;
* Please consider using the examples below as a template:
  * Example to follow&#x20;
  * Example to follow&#x20;
* These proposals are on-chain, meaning any on-chain instructions included in the proposal will be executed upon conclusion of the vote. If approved by governance, the amount of the highest possible payout will be transferred to the KPI Council multi-sig. These funds will be held in escrow until proven achievement of pre-defined KPIs is approved by the [KPI Council](https://community.sommelier.finance/t/discussion-about-establishment-of-a-kpi-incentives-council-kpic/1082) at the end of each quarter.

## **Protocol Upgrade Proposal**

* Software upgrades to the Sommelier chain are handled through an upgrade proposal, which defines a "plan name" of the expected upgrade and a block height at which the upgrade must be executed.&#x20;
* At the upgrade height, all nodes will panic if they do not have code for the defined upgrade plan. Once validators replace their old binary with the new binary containing the upgrade code (whether manually or through a pre-established setup using a tool like Cosmovisor), the upgrade handler will be executed and the chain will resume producing blocks when enough validators have upgraded.&#x20;
* There is also a proposal type for canceling an upgrade, though it can only work if there is enough time for the voting period to pass before the upgrade height.

### **Parameter Change Proposal**

* Each module in a Cosmos SDK chain may choose to define a set of parameters -- values that affect the module which can be adjusted via governance without requiring an upgrade. Any of these parameters may be set to new values in a proposal following the process outlined [here](https://hub.cosmos.network/main/governance/formatting.html#param-change).

[^1]: This was in the text but I don't think this is an actual proposal type, strategists have just been creating grant or cellar activation proposals&#x20;



[^2]: 
