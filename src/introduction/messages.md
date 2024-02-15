# Messages

In distributed systems nodes exchange messanges to communicate. But as OS arch may be different (Big endian vs Little endian), language representation of strings can be different (0-terminated or not), it is necessary to use 
serialization/deserialization scheme.

It can be simple - prepend with the number of further bytes, and cast further bytes with known rules.
It can be complex like
* json
* Protobuf/Bincode/Apache Avro/Thrift/FlatBuffers/Postcard[^benchmarks]

Some require Interface Definition Language (IDL) others are schemaless.

Besides performance, there are the following considerations
* community and maintainability (some formats emerge as a research effort and are abandoned afterwards)
* standards behind formats and interoperability between different languages
* underlying format pluggability (e.g. serde-rs has tow layers - it serializes first to format-agnostic representation and then the representation can be serialized to json/postcard/bincode etc)
* ease of debug (text-based json is easier to debug than binary protobuf)


[^benchmarks]: you can check current [benchmarks on serialization/deserialization in Rust](https://github.com/djkoloski/rust_serialization_benchmark) 