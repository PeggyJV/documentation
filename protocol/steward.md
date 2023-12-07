# Steward

Steward provides the interface strategists use to manage Cellar contracts. Each validator runs their own instance of the Steward process and receives contract call data over an authenticated connection. After confirming the caller's authorization to interact with the target contract, Steward submits the call to the Sommelier chain. A quorum of these submissions must occur on chain in order for any call to be finally executed on the target contract. This model allows strategists to have access to contract functionality necessary to manage investment while preventing a malicious or compromised strategist from having direct access to user funds.&#x20;

Documentation for the current version of steward can be found here:

[https://github.com/PeggyJV/steward/tree/3.x-main/docs](https://github.com/PeggyJV/steward/tree/3.x-main/docs)

### Deployment

Unlike most chains, Sommelier requires a server API sidecar process (Steward) to be accessible from the internet. Communications to Steward are protected using mutually-authenticated TLS. During our bootstrapping phase, Steward has a harcoded certificate authority to allow Seven Seas client certificates, though the protocol will be expanded to allow new strategist integrators via governance in the v7 upgrade, which will also include an authorization layer mapping specific strategist integrators with the cellar addresses for which they have been approved. For validators, they must create their own self-signed CA and server certificates by following the instructions in the "steward-registry" repo and adding their data via pull request:

[https://github.com/PeggyJV/steward-registry](https://github.com/PeggyJV/steward-registry)

The v7 upgrade will also provide a method for handling this on-chain and will remove the necessity of the registry.

The Steward server process must be on a publicly available port, the default of which is 5734, but can be changed as necessary. The fully qualified URL and port number to access your steward instance should be included in your steward-registry pull request.

### API

Steward's API is a GRPC interface which exposes a defined subset of contract functionality as protobufs. It does not accept raw encoded Ethereum calls, but rather accepts the parameters of function calls and handles the encoding itself, to ensure only authorized functions and in some cases bounded values are used. Existing Sommelier cellars are built using a specific architecture developed by a team of smart contract developers the protocol team has coordinated with, but any new cellar architecture approved by governance can be supported. However, due to the nature of how the API permissions and encodes cellar calls, code updates to Steward and a subsequent validator update of the Steward process is necessary before a new cellar architecture will be functional.

### Strategists

Strategists must submit calls to each validator's Steward instance using the defined GRPC API. In order for a call to be made to an Ethereum cellar, these updates must be sent to the Steward instance's of a consensus power of validators. The current architecture defines a periodic voting window every 10 blocks, and the strategist should submit their calls at the beginning of this window to allow for some latency. A call that reverts will remain in the gravity module until it times out, and depending on the revert being either transient or permanent, it may be retried at any time until it times out. The current timeout period is expected to be roughly 12 hours. The v7 upgrade will modify some of these requirements, and rather than timing submissions during voting windows, calls will execute at a height specified by the strategist.

The list of Steward endpoints for each validator can be found in the steward-registry:

[https://github.com/PeggyJV/steward-registry](https://github.com/PeggyJV/steward-registry)
