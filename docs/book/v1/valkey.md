# Valkey usage

Valkey is an open source (BSD) high-performance key/value datastore that supports a variety of workloads such as caching, message queues and can act as a primary database.

The following commands can be run in the CLI to interact with Valkey.
To enter the CLI, run:

```shell
valkey-cli
```

## Utility Commands

List all keys matching a pattern.

```shell
KEYS *
```

Get server information and statistics:

```shell
INFO
```

Check if the server is running:

```shell
PING
```

Delete all keys in all databases:

```shell
FLUSHALL
```

Check data type stored at a specific key (Possible types: string, list, set, zset, hash, stream):

```shell
TYPE keyName
```

## Key-Value Operations

Set a string key to a value:

```shell
SET keyName "keyValue"
```

Get the value of a key:

```shell
GET keyName
```

Delete one or more keys:

```shell
DEL keyName1 keyName2
```

## Stream Commands

Read entries from a stream oldest to newest (revers '-' and '+' to reverse order):

```shell
XRANGE streamName - +
```

Delete stream:

```shell
DEL streamName
```

Remove all stream entries while also keeping the stream key:

```shell
XTRIM streamName MAXLEN 0
```

Delete a specific entry:

```shell
XDEL streamName <entryId>
```
