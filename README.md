# GoSSIP - Simple Go messaging and data synchronization framework

## Motivation
GoSSIP wants to be the synchronization layer for your distributed data application. Let's say you're building the next $19B chat app and you need a way to orchestrate multiple writes to a piece of shared state, like a message queue. Traditionally this is done by having a single central server which accepts updates from a client and then re-distributes that update to all other clients. The server also takes care of ordering messages as they come in. These tasks make up the two important parts of synchronization: Causality, and Replication. In today's world of massive connectivity, a distributed approach to data synchronization can save CPU power and bandwidth (and thus money).

## Replication
This one is is straightforward. We have a bunch of nodes and we want each node to receive (or have the option to receive) every message that flows through the system.

## Causality
It's important to know the ordering of messages in a distributed system (after all, you wouldn't want your users' messages appearing out of order right?). A full ordering is impossible without external synchronization (back to a central server), but we can achieve a partial ordering, enough to say that "Event 1 came before Event 2" in many cases by sending some carefully crafted metadata along with each message.

## Message

## Node

## Network

# FAQ

### That's a lot of keyword spam, what does this do for me?

Takes care of data replication, ordering, and synchronization between lots of nodes (servers, clients, et). Provides a friendly API which makes it easy to build a realtime data application.

### What's in a name?

GoSSIP stands for Go Stable Simple 