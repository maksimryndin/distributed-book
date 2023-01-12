# Practice: dissecting network packets

Some serialialization formats (like FlatBuffers and Cap'n Proto) structure the data [the same way in-memory and on the wire, thus avoiding costly encode/decode steps](https://capnproto.org/news/2014-06-17-capnproto-flatbuffers-sbe.html). Also use of arena allocation allows to achieve significant performance gains (see [Protobuf Changelog](https://github.com/protocolbuffers/protobuf/blob/main/CHANGES.txt#L2836))

Create general Packet structure which can have defined fields
Field has its serialization and deserialization logic
Look at serde, Scapy