# API Specification

GoSSIP is a stream oriented framework with snapshotting utilities. This means that all API calls return streams, but you may convert a stream to a read-only snapshot at any time.

For most use cases use of the streams will result in better performance.

##