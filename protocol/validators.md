# Validators

Validators run Sommelier nodes and vote to achieve consensus for every block produced in the network. Becoming a validator is permissionless -- anyone can become a validator by running the appropriate software and registering themselves with the protocol. A validator's voting power towards achieving consensus is determined by the amount of SOMM that has been delegated to them by stakers of the SOMM token. Validators will receive a percentage of SOMM paid to stakers determined by the commission they have configured. Stakers generally prefer validators who are engaged with the community, participate in governance, and update their software in a timely fashion. Running properly configured services and being responsive as a validator is a key component in the success of the Sommelier protocol.

### What software must a validator run?

The Sommelier protocol requires three distinct software processes to run. Each serves a different purpose in enabling end-to-end functionality of the protocol. Reliability and uptime for these processes is critical.

#### Sommelier node

The Sommelier node is the Cosmos SDK-based full node that is required for participation in the network and achieving protocol consensus. Like all other Cosmos proof-of-stake chains, running this software is the primary responsibility of validators in order for the network to continue producing new blocks and process messages.

The node software and releases can be found here:

[https://github.com/PeggyJV/sommelier](https://github.com/PeggyJV/sommelier)

#### Gravity Bridge orchestrator

The Sommelier protocol communicates with Ethereum using a fork of the Gravity Bridge. The components of this system include a smart contract on Ethereum called the Gravity contract, and a module in the Sommelier node called the "gravity" module which is responsible for coordinating state between Cosmos and Ethereum.

This coordination is achieved by each validator running an orchestrator process. When registering with the chain, a validator will set up two "delegate" keys: one being a Cosmos account that will be used for sending orchestration messages to the chain, and one being an Ethereum account for interacting with the Gravity contract. The orchestrator serves two main functions:

* Signer: Events originating on the Cosmos side (updates to cellars, updates to the validator set data on the Gravity contract, bridging tokens to Ethereum) are recorded in the chain state. The orchestrator process will observe these events and create a signature confirming them (using their Ethereum delegate key) and submit them to the chain (using their Cosmos delegate key). Once enough of these confirmations have been received based on a consensus of staked power by validator, these signatures will be sent along to a call to the Gravity contract in order for the event to execute on Ethereum.
* Oracle: Events originating on the Ethereum side (bridging tokens to Sommelier, events emitted from successful Gravity calls) are observed by the oracle. Validators confirming their observation of these events is what closes the loop on the end-to-end process of Cosmos-originated events or bridging calls from Ethereum, and allows the bridge to continue to the next nonce and process further events.

It is recommended to run the orchestrator by using one of the operating modes of the Steward process.

Steward can be found here:\
\
[https://github.com/PeggyJV/steward](https://github.com/PeggyJV/steward)

With documentation found here:\
\
[https://github.com/PeggyJV/steward/tree/3.x-main/docs](https://github.com/PeggyJV/steward/tree/3.x-main/docs)

#### Strategist API server

Strategists do not need to run code on-chain -- they can use machine learning, big data, or any other number of systems, and then submit their update recommendations based on their proprietary technology and algorithms. In order to make this possible, each validator must run a strategist server by which a strategist can communicate their update recommendations for approval by the validator set. The reference implementation of this server is called Steward, which by default takes these recommendations at face value and executes them.

The strategist server serves a number of purposes:

* Authentication and authorization: strategists communicate with the strategist server using mutually-authenticated TLS. Currently, Steward is pre-populated with the client-certificate CA for Seven Seas, our launch partner during the proof-of-concept phase of the network, but will in the v7 upgrade retrieve these CAs from on-chain data. In addition, permissions to update specific cellars are assigned to specific strategists (again, currently only Seven Seas). This same v7 upgrade will also allow for mechanisms to customize these strategist update subscriptions for each cellar according to the validator's wishes.
* API specification: cellar contracts may have many different functions, some of which are the province of the individual strategist, and some of which are more sensitive and may require the approval of SOMM holders via governance. The strategist server defines what functions can be called in this manner by specifying a GRPC API for strategists to call, and then translating those calls into executable bytes for the Ethereum contract. Currently, calls which are not specified in Steward may be executed through reaching consensus via a text governance proposal and coordinating manually, but the v7 upgrade will create a mechanism for specifying calls scoped to governance.

Steward is provided as a reference implementation of the strategist server. Validators wishing to use the default behavior can run Steward without modification. It is not required that a validator specifically run Steward, as they could fork and modify it or create their own implementation, but it is required that they will expose the same GRPC interface a strategist expects to call and that their process actively participates in the cellar update process. Future updates to the protocol will introduce slashing penalties for non-participation in this process, as reaching consensus on cellar updates is required for Sommelier to function.

Steward can be found here:\
\
[https://github.com/PeggyJV/steward](https://github.com/PeggyJV/steward)

With documentation found here:\
\
[https://github.com/PeggyJV/steward/tree/main/docs](https://github.com/PeggyJV/steward/tree/main/docs)

### Slashing

Penalties for validators that fail to participate or misbehave in a Cosmos proof-of-stake network are referred to as slashing. These can range from token penalties as a percentage of delegated stake (all stakers with the validator will lose this percentage) to the validator being jailed (temporarily removed from the consensus set and rewards) or tombstoned (permanently removed).

During the testing, bootstrapping, and proof of concept phases of the Sommelier protocol, token penalties are not currently being issued when slashed. Failing to have your orchestrator process keep pace with signing events can result in being jailed. Future updates will introduce penalties for the standard Cosmos SDK slashing cases of downtime or double signing, and protocol-specific penalties for failing to participate in the cellar update process.

### Communication channels

Telegram channel:

[https://t.me/+wLi1PfO7SjE0MTZh](https://t.me/+wLi1PfO7SjE0MTZh)

Discord server:\
\
[https://discord.com/invite/ZcAYgSBxvY](https://discord.com/invite/ZcAYgSBxvY)

