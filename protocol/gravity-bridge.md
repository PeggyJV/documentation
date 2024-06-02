# Gravity Bridge

Sommelier uses a fork of the Gravity Bridge to communicate with and send tokens to/from Ethereum. Below is a high-level description of how it works -- you can find further details about the architecture and design at:\
\
[https://github.com/PeggyJV/gravity-bridge/tree/main/docs](https://github.com/PeggyJV/gravity-bridge/tree/main/docs)

The Gravity Bridge consists of three elements:

* Gravity contract: an Ethereum smart contract that maintains the Ethereum side of the bridge. Sommelier's deployed bridge contract can be located here:\
  \
  [https://etherscan.io/address/0x69592e6f9d21989a043646fE8225da2600e5A0f7](https://etherscan.io/address/0x69592e6f9d21989a043646fE8225da2600e5A0f7)
* Orchestrators: each validator runs an "orchestrator" process, which is responsible for reaching consensus on the Cosmos side for bridge operations, and acting as an oracle to observe the Ethereum side to confirm state changes
* Relayer: as the Gravity contract uses signatures contained in bridging function arguments to assert their validity, relaying transactions to the Gravity contract is permissionless. Whoever chooses to run a relayer will pay the gas cost of submitting Cosmos-to-Ethereum calls to the Gravity contract. Since only one transaction per nonce can be accepted, multiple relayers will currently have a race condition where one succeeds while the others revert.

Each of the five bridging actions is described below.

### Cosmos-to-Ethereum

The lifecycle of a Cosmos-to-Ethereum call is as follows:

1. The Cosmos chain indicates in its state that a bridging action must occur.
2. The orchestrator processes of each validator observe the Cosmos chain state, sign the action using their Ethereum delegate key (an Ethereum-side identifier set using their validator credentials), and submit this signature to the chain.
3. When a consensus of signatures for an action have been received, it enters a relayable state.
4. One or more relayer processes will attempt to submit the action to the Gravity contract.
5. Upon success of the action, orchestrator processes will act as an oracle to observe the event on Ethereum, and then submit a confirmation to the chain.
6. When a consensus of validators have confirmed the Ethereum event has been observed, it can move on to confirming the next nonce.

#### Logic calls

A logic call consists of a target contract address, an encoded Ethereum contract call, an "invalidation scope" which hashes elements of the logic call, and an "invalidation nonce" which prevents replaying calls from the same scope. Sommelier uses a module called "cork" and the Steward process to allow strategists to submit logic calls to be executed on the Gravity bridge.

#### Batch transaction

A batch transaction groups together requests to bridge Cosmos-side tokens to Ethereum and submits them as a single contract call. A batch may contain one or more transactions. Each element of a batch transaction will have a user-supplied fee with a denomination that is the same as the token being bridged.

A web front-end for the bridge can be found here:

[https://app.sommelier.finance/bridge](https://app.sommelier.finance/bridge)

#### Deploy ERC-20

Any native Cosmos asset on the Sommelier side can be permissionlessly deployed to Ethereum by using the deploy ERC-20 function. It will use metadata from the bank module to populate the metadata of the ERC-20. This can happen only once per denomination, and the Gravity contract will treat the deployed ERC-20 as canonical for the Cosmos-side asset and own MAX\_INT of its supply.

#### Validator set updates

The Gravity contracts understand the respective powers of each validator, tied to their Ethereum delegate key, based on information submitted from the Cosmos chain in a valset update. This was initially bootstrapped when the chain was first deployed, and changes any time the delegated power of validators shifts by more than 5%.

### Ethereum-to-Cosmos

Ethereum-to-Cosmos transactions only require the observation of the oracle in the orchestrator processes to be confirmed by the chain.

#### Send to Cosmos

Tokens can be bridged from Ethereum by calling the send to Cosmos function, which simply requires the ERC-20 token contract address, the amount to send, and the destination Sommelier account. If directly interacting with the contract, note that the encoding of the Sommelier address within the bytes32 is not a byte encoding of the bech 32 string -- it is expecting the raw 20-byte representation of the address in the rightmost bits, and padded on the left with zeroes.

A web front-end for the bridge can be found here:

[https://app.sommelier.finance/bridge](https://app.sommelier.finance/bridge)
