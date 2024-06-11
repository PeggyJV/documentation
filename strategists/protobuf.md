# Protobuf

If you are familiar with the use of Protocol Bufffers (protobuf) and gRPC, you can skip this page.

> If you are interested in a deeper dive into Protocol Buffers, check out the [official documentation](https://protobuf.dev).

In short, protobuf messages define types that can then be generated into any programming language. This allows for a common interface between different languages and platforms and saves time by automatically generating code for message serialization and deserialization and API client/server code.

The Steward API is defined by a protobuf package and can be used with a gRPC client. Strategists must generate proto bindings and a client from the Steward API protobuf files for their preferred language using the [protoc compiler](https://grpc.io/docs/protoc-installation/) or [buf](https://buf.build).

Please feel free to reach out to the Sommelier team for assistance with generating the necessary bindings in your language of choice if required.
