# Messenger Configuration

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
        'factories'> [
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
