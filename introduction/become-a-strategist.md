# Become a Strategist

Sommelier Finance is a blockchain platform designed to facilitate the execution of decentralized finance (DeFi) strategies on the Ethereum network. Strategists play a crucial role in this ecosystem, as they are responsible for determining optimal rebalance decisions off-chain and submitting them to the Sommelier validator set. If consensus is reached among the validators, the rebalance instruction is executed on the Ethereum smart contract. In this guide, we will discuss the steps and background necessary to become a strategist on the Sommelier platform.

## Desired Background

1. Blockchain and DeFi Knowledge: A strong understanding of blockchain technology, Ethereum, smart contracts, and the DeFi ecosystem is essential for becoming a successful strategist on Sommelier. Familiarity with the mechanics and functionalities of various DeFi protocols, such as decentralized exchanges, lending platforms, and yield farming is .
2. Technical Skills: Proficiency in programming languages, such as Python or JavaScript, is crucial for developing and testing trading strategies on the platform. Experience with web3 tools and libraries, such as Web3.js, Ether.js, or Truffle, is also beneficial.
3. Finance and Trading Experience: A background in traditional finance or cryptocurrency trading, including knowledge of technical and fundamental analysis, risk management, and portfolio management, will help you make informed decisions while optimizing trading strategies.

While the above background is recommended, it is not required for becoming a strategist. In particular, prospective Strategists that have backgrounds in finance and DeFi experience but lack programming experience, for example, may integrate into the Sommelier platform using a strategist integration service (see below)



## Steps for Onboarding to the Platform as a Strategist

The steps to becoming a strategist on the Sommelier protocol are:&#x20;

1. Make a forum post outlining relevant background experience and why the Sommelier community should accept you as a strategist. Please provide as much information as possible about your team as strategist reputation and track-record is one of the factors most heavily evaluated during the governance process. Please include links to your online presence as well.
2. Engage with the community to answer questions and respond to feedback
3. If reception is positive, make a governance proposal as outlined in the "Governance Tab"

If the proposal passes and Governance has accepted you as a strategist, you are now able to operate Cellars through Steward. There is another Governance process for getting a strategy accepted on chain, outlined in the "Governance Tab."&#x20;

## Steps for Creating a Strategy

Making a Cellar smart contract eligible to receive rebalance instructions from the Sommelier validator requires the following steps

1.  Make a forum post describing the strategy in detail as well as providing any backtesting or live performance results. The forum post is meant to convey all necessary information about the strategy to the Community. This information includes:

    * The set of protocols that the strategy will access&#x20;
    * The assets that the strategy will take exposure to
    * Who the strategy provider will be
    * The fee structure (platform/performance fee)
    * The success and failure conditions of the strategy
    * Summary statistics on the expected performance (Sharpe ratio, maximum drawdowns, etc.)

    Note that at this stage, the Cellar smart contract must already be deployed and the parameters fixed. Additionally, Gravity must be the owner of the Cellar.
2. Make a Governance proposal to accept the Cellar on-chain. The Cellar smart contract that the proposal points to must be the same one described in the forum post. This is a security feature to ensure that the Sommelier community has had adequate time to understand the behavior of the smart contract before voting.

If the Cellar proposal passes, the Sommelier chain will support sending rebalance instructions to the Cellar through the Steward API.



## Strategist Integration Service

A strategist integration service is when an existing strategist ("Integrator") supports the integration of a new strategist by abstracting the blockchain and smart contract details away as an API.&#x20;

By presenting prospective strategists with a simplified interface, the Integrator reduces the barrier of entry to operating strategies on Sommelier.

Currently, the primary Integrator on Sommelier is [Seven Seas Capital](https://sevenseas.capital/). For more information, reach out directly to the Seven Seas team via email, Discord, or Twitter.



\
