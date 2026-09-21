# Valkey usage

## Summary

Quick reference for the Valkey CLI commands used to inspect and manage the
`messages` and `failed` streams that back the queue.

## Details

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

Read entries from a stream, sorted from oldest to newest (switch '-' and '+' to reverse order):

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

## FAQ

**Q: How do I open an interactive Valkey session?**

A: Run `valkey-cli` on the server; it drops you into the CLI used for every command
on this page.

**Q: How do I read the queue's stream without removing anything?**

A: Use `XRANGE streamName - +` to read entries oldest to newest (switch `-`/`+` to
reverse the order); it doesn't delete anything.

**Q: How do I clear a stream without deleting the key itself?**

A: `XTRIM streamName MAXLEN 0` removes all entries but keeps the stream key, unlike
`DEL streamName` which removes the key entirely.
