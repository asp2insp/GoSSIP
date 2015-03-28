# API Specification

GoSSIP is a stream oriented framework with snapshotting utilities. This means that all API calls return streams, but you may convert a stream to a read-only snapshot at any time.

For most use cases use of the streams will result in better performance.

Each connection to GoSSIP provides two streams: IN and OUT. You will probably want to structure your application to fork or filter IN a number of times, then mergeInto OUT once you have processed or generated data.

# Streams
GoSSIP is entirely stream oriented. All data is immutable, and flows through the app in streams. In the Go implementation, a stream wraps a channel in a friendly API.

## mergeInto(stream)
## fork()
## split()
## limit??
## close()
## drain()
## insert(message)

## consume(handler)


Not sure if these are filters or stream apis. I'm leaning towards filters.
## onMessage
## onOpen
## onClose
## onChange
## onInsert
## onDelete
## onAny


# Filters

## builder.[filter]+.build
## from
## until
## property
## authorType
## before
## after
## around
## sample
## dedup

Each returns a filtered stream. filtered streams are lazily built so each message is only queried once per filtered view. Building a filter on a stream does not change the underlying stream.

when a filtered view is created, it runs through all available historical data, then continues to update whenever new data is available.

Filters are always composable and will warn when you try to compose contradictory filters.

If the PubSideFilters setting is enabled, filters will eventually be pushed over connections to reduce the number of messages sent.

Filtered views are immutable once built.

# Modifiers
Modifiers edit messages in flight, one at a time.

## addTag
## removeTag
## anonymize
## clearMetadata
## clearPayload
## editPayload

like aggregators, modifiers take a stream and produce a new stream. 

# Aggregators
An aggregator processes a stream of events and collapses them into a read-only data structure. Each aggregator has its own stream that allows filtering, subscription, and yes, aggregation so that you can express complex transformations from simple building blocks.

## count
## sum
## group
## unique
## average
## stdev
## max
## min
## snapshot


# Use Cases

## 1-N
```
func OneToN(in stream, n int) (output stream[]) {
  output = make(stream[], n)
  for i := 0; i < n; i++ {
    output[i] = in.fork()
  }
  return
}
```

## Fanout
```
func Fanout(in stream, n int) (output stream[]) {
  output = make(stream[], n)
  for i := 0; i < n; i++ {
    output[i] = in.split()
  }
  return
}
```

## Fanin
```
func Fanin(inputs stream[]) (output stream) {
  output = new stream
  for i := 0; i < len(inputs); i++ {
    inputs[i].mergeInto(output)
  }
  return
}
```

# Example - Group Chat

```
Participant struct {
  userId string
  conn GoSSIP
}

Conversation struct {
  IN, OUT stream
  id string
}

func (*Participant p) init(c GoSSIP) {
  p.userId = uuid.Uuid4()
  p.conn = c
}

func (*Participant p) connectTo(convoId string) {
  c := new Conversation
  c.IN = p.conn.IN.filter().tag("convoId", convoId).stream()
  c.IN.fork().filter().dedup().stream().consume(c.displayMessage)
  c.IN.fork().consume(p.storeToDB)
  c.OUT.mergeInto(p.conn.out)
}

func (*Conversation c) sendMessage(m Message) {
  c.IN.insert(m)
  c.OUT.insert(m)
}
```