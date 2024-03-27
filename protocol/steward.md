# Steward

Steward exposes the Strategist API, an interface strategists use to manage Cellar contracts. Each validator runs their own instance of the Steward process and receives contract call data over an authenticated connection. After confirming the caller's authorization to interact with the target contract, Steward submits the call to the Sommelier chain. A quorum of these submissions must occur on chain in order for any call to be finally executed on the target contract. This model allows strategists to have access to contract functionality necessary to manage Cellar positions while preventing a malicious or compromised strategist from having direct access to user funds.&#x20;

Documentation for the current version of steward can be found here:

[https://github.com/PeggyJV/steward/tree/main/docs](https://github.com/PeggyJV/steward/tree/main/docs)

### Deployment

For a detailed guide to deploying steward for a validator node, please see the [Steward for Validators](https://github.com/PeggyJV/steward/blob/main/docs/02-StewardForValidators.md) documentation.

### Strategist API

Steward defines an [API using protobuf](https://github.com/PeggyJV/steward/blob/main/proto/steward/v4/steward.proto) of Cellar functionality available to strategists. The API is exposed as a gRPC interface. It does not accept raw encoded Ethereum calls, but rather accepts the parameters of function calls and handles the encoding itself, to ensure only authorized functions and in some cases bounded values are used. Existing Sommelier cellars are built using a specific architecture developed by a team of smart contract developers the protocol team has coordinated with, but any new cellar architecture approved by governance can be supported. However, due to the nature of how the API permissions and encodes cellar calls, code updates to Steward and a subsequent validator update of the Steward process is necessary before a new cellar architecture will be functional.

### Steward Registry (Pubsub module)

The Sommelier chain runs the `x/pubsub` module which acts as a registry of strategists (Publishers), steward instances (Subscribers), and subscriptions. Subscriptions are the mapping between a strategist's registered intent to publish call data for a particular Cellar (subscription ID) and a steward's desire to receive call data from a particular strategist. 

The pubsub module define a number of entities that represent the different participants and their intentions in the Sommelier protocol:

`Publisher` - Represents a Strategist. Contains the domain from which they will submit contract calls, their self-signed Certificate Authority, and the somm address representing their on-chain identity.

`Subscriber` - Represents a steward instance. Contains the endpoint of the Steward server that Publishers make calls to, its self-signed Certificate Authority, and its somm address.  

`PublisherIntent` - Represents a Cellar for a which a strategist intends to submit calls. Defines a "subscription ID" which is the chain ID where the Cellar resides and the contract address of the cellar concatenated with a ':' (e.g. "42161:0xC47bB288178Ea40bF520a91826a3DEE9e0DbFA4C" is RY ETH on Arbitrum).

`SubscriberIntent` - Represents an a steward's intent to listen to a Publisher for calls to a particular Cellar.

`DefaultSubscriptions` - Represents the default publisher stewards should accept calls from for a particular Cellar. Strategists use Default Subscritions in combination with SubscriberIntents to get an accurate list of steward server endpoints to which they should publish calls.

Publishers who create the original Cellar approval proposal for a particular Cellar are the "default" publisher, and all steward instances will automatically trust this Publisher for that particular Cellar. However, it is entirely possible for enough consensus power to configure their steward to listen to an alternative strategist for any number of Cellars.

New or migrating validators must [add or update a Subscriber](https://github.com/PeggyJV/steward/blob/main/docs/05-PubsubForValidators.md#registering-steward-as-a-subscriber) on chain so that their steward instance is reachable.

