# Messenger Configuration

## Summary

Reference for `config/autoload/messenger.local.php`: the Redis transports Symfony
Messenger uses for new and failed messages, their retry strategy, and the two
message streams (`messages`, `failed`) they map to.

## Details

```php
return [
    'symfony'      => [
        'messenger' => [
            'transports'        => [
                'redis_transport' => [
                    // specifies the Redis server and stream/key
                    // messages is the main stream where new messages are initially stored
                    'dsn'            => 'redis://127.0.0.1:6379/messages',
                    // defines which serializer to use to encode/decode messages
                    'serializer'     => SymfonySerializer::class,
                    'retry_strategy' => [
                        // maximum number of retry attempts before moving a message to the failure transport
                        'max_retries' => 3,
                        // initial delay before retrying a failed message, in milliseconds
                        'delay'       => 1000,
                        // each retry’s delay is multiplied by this factor
                        'multiplier'  => 2,
                        // maximum delay allowed between retries, 0 means unlimited or default behavior
                        'max_delay'   => 0,
                    ],
                ],
                // defines a transport named failed, used to store messages that cannot be delivered after retries.
                'failed'          => [
                    // specifies the Redis server and stream/key
                    'dsn'        => 'redis://127.0.0.1:6379/failed',
                    // defines which serializer to use to encode/decode messages
                    'serializer' => SymfonySerializer::class,
                ],
            ],
            // tells Symfony Messenger to send messages that exceed retry limits to the failed transport
            'failure_transport' => 'failed',
        ],
    ],
    'dependencies' => [
        'factories' => [
            'redis_transport'        => [TransportFactory::class, 'redis_transport'],
            'failed'                 => [TransportFactory::class, 'failed'],
            SymfonySerializer::class => fn(ContainerInterface $container) => new PhpSerializer(),
        ],
    ],
];
```

## Main queue stream (`messages`)

`messages` stream is the main queue where all new messages are initially stored. The Messenger worker consume messages from this stream and attempt to process them according to the application logic.

## Dead Letter Queue (DLQ)

DLQ is a dedicated transport where messages are sent when they fail to be processed after a configured number of retries. Each transport can define a retry_strategy specifying the maximum number of retry attempts, delays between retries, and exponential backoff rules. When a message exceeds the allowed retries, it is automatically forwarded to the failure transport and stored in `failed` stream, ensuring that failed messages do not block the queue.

## Application-level retry delays (`fail-safe`)

`config/autoload/local.php` also defines a separate `fail-safe` schedule, used to
delay re-adding a failed message to the queue:

```php
'fail-safe' => [
    'first_retry'  => 3600000, // 1h
    'second_retry' => 43200000, // 12h
    'third_retry'  => 86400000, // 24h
],
```

This is independent of the transport-level `retry_strategy` above.

## FAQ

**Q: Where do the transport-level retry settings live?**

A: In `config/autoload/messenger.local.php`, under
`symfony.messenger.transports.redis_transport.retry_strategy`.

**Q: What happens once `max_retries` is exceeded?**

A: The message is forwarded to the `failed` transport, defined by
`failure_transport`, and stored in the `failed` Redis stream.

**Q: Is `retry_strategy` the only retry configuration in the project?**

A: No — `config/autoload/local.php` also defines an independent `fail-safe` schedule
(`first_retry`, `second_retry`, `third_retry`) for delaying re-queued messages after
a processing error.
