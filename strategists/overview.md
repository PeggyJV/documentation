# Overview

## Index
- [Overview](#overview)
- [Protobuf](./protobuf.md) 
- [Requests](./requests.md)
- [Cellar Functions](./cellar_functions.md) 
- [Adaptor Reference](./adaptor_reference.md)

Strategists are users or organizations responsible for submitting parameters for Cellar rebalances and other contract calls to Sommelier through the Steward API. To accomplish this they use the [Steward API](). The Steward API is defined by a protobuf package and can be used with a gRPC client. Strategists must generate proto bindings and a client from the Steward API protobuf files for their preferred language.

Strategists must also generate a Certificate Authority and use it to generate a self-signed certificate, which is the representation of a strategist's identity as a Publisher on the Sommelier chain required to create a trusted connection with the Steward API.

Requests to the Steward API use the type `ScheduleRequest`. This request contains contract call data and information used by the Sommelier chain to determine the destination of the contract and the timing of transaction execution. The response type `ScheduleResponse` contains information that can be used to query the Sommelier chain in order to confirm that a call, referred to on-chain as a Cork, was submitted and scheduled successfully. 

```protobuf
// Defined in steward/proto/steward/v4/steward.proto

// Represents a scheduled function call to a particular Cellar
message ScheduleRequest {
    // The ID (currently simply an Ethereum address) of the target Cellar
    string cellar_id = 1;
    // The Sommelier block height at which to schedule the contract call
    uint64 block_height = 2;
    // The data from which the desired contract function will be encoded
    oneof call_data {
        AaveV2Stablecoin aave_v2_stablecoin = 3;
        CellarV1 cellar_v1 = 4;
        CellarV2 cellar_v2 = 5;
        CellarV2_2 cellar_v2_2 = 6;
        CellarV2_5 cellar_v2_5 = 7;
    }
    // The ID of the chain on which the target Cellar resides
    uint64 chain_id = 8;
    // The unix timestamp deadline for the contract call to be executed.
    // Ignored for Ethereum Cellars.
    uint64 deadline = 9;
}

message ScheduleResponse {
    // The hex encoded ID of the scheduled cork
    string id = 1;
    // The ID of the chain on which the target Cellar resides
    uint64 chain_id = 2;
}
```

The request's `call_data` field contains the actual contract call arguments. Most strategists will be using the `CellarV2_5` call data type, which represents calls structured after the Cellar v2.5 architecture. 

Strategists can execute a single function call or multiple function calls in the same transaction using the Multicall call type. The most important and complex function callable in Cellar v2.5 is `CallOnAdaptor`. It allows one or more adaptor function calls to be executed against a large selection of Sommelier adaptors defined in the Steward API.

