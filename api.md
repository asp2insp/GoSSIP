# API Specification

GoSSIP is a stream oriented framework with snapshotting utilities. This means that all API calls return streams, but you may convert a stream to a read-only snapshot at any time.

For most use cases use of the streams will result in better performance.

## builder.[filter]+.build
## from
## until
## property
## authorType
## before
## after
## around

Each returns a filtered stream. filtered streams are lazily built so each message is only queried once per filtered view.

when a filtered view is created, it runs through all available historical data, then continues to update whenever new data is available.

Filters are always composable and will warn when you try to compose contradictory filters.

If the PubSideFilters setting is enabled, filters will eventually be pushed over connections to reduce the number of messages sent.

Filtered views are immutable once built.