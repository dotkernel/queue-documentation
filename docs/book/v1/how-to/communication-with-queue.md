# COMUNICATE WITH QUEUE

Communication with the  [`Dotkernel Queue`](https://github.com/dotkernel/queue) can be achieved in two different ways: procedural and object-oriented.

## Procedural approach

The main advantage of the procedural code is its simplicity, it directly connects to the queue server, sends the prepared data, and then closes the connection. In this example, the code opens a TCP connection to `localhost:8556`, checks if the connection is successful, sends data in JSON format, and closes the socket. This makes the logic easy to follow and quick to implement. The disadvantage of this approach is the difficulty of reusing and extending the code. As the complexity of the project increases by repeatedly using this approach, the code becomes more difficult to maintain and not as flexible compared to the object-oriented approach.

```shell
// collect the PAYLOAD that will be sent to Dotkernel Queue 
$data = $anyTypeOfData;

// open TCP connection to Dotkernel Queue 
// replace localhost with the IP of the Dotkernel Queue 
// replace 8556 with the port number 
$client = stream_socket_client("tcp://localhost:8556", $errno, $errstr, 30);

if (! $client) {
    // log errors
    error_log("Connection error: $errstr ($errno)");
} else {
    // send the PAYLOAD to Dotkernel Queue 
    fwrite($client, json_encode($data) . "\n");
    // make sure your message ends with a new line
    // this allows the stream to know when the message is complete
    // without it, the server might keep waiting, and not process what was sent

    fclose($client);
    // close connection 
}
```

## Object-oriented approach

Using object-oriented approach, queue is accessed through dedicated classes and objects. Instead of calling functions directly, you instantiate objects that encapsulate the behavior of the queue. This approach offers reusability, maintainability, and cleaner separation of responsibilities, which is especially beneficial in larger applications.

To implement this approach you need to follow a few simple steps:

## Step 1 NotificationSystem module

Create a new directory under the Core directory with the name NotificationSystem or any other name.

Inside it, create a new `ConfigProvider.php`, a new `NotificationService.php` and then copy the code below.

ConfigProvider code:

```shell
<?php

declare(strict_types=1);

namespace Core\NotificationSystem;

use Core\NotificationSystem\Service\NotificationService;
use Dot\DependencyInjection\Factory\AttributedServiceFactory;

/**
 * @phpstan-type ConfigType array{
 *      dependencies: DependenciesType,
 * }
 * @phpstan-type DependenciesType array{
 *       factories: array<class-string, class-string>,
 * }
 */
class ConfigProvider
{
    /**
     * @return ConfigType
     */
    public function __invoke(): array
    {
        return [
            'dependencies' => $this->getDependencies(),
        ];
    }

    /**
     * @return DependenciesType
     */
    public function getDependencies(): array
    {
        return [
            'factories' => [
                NotificationService::class => AttributedServiceFactory::class,
            ],
        ];
    }
}
```

Service code:

```shell
<?php

declare(strict_types=1);

namespace Core\NotificationSystem\Service;

use Core\User\Entity\User;
use Dot\DependencyInjection\Attribute\Inject;
use Laminas\Json\Encoder;
use Socket\Raw\Factory;
use Socket\Raw\Socket;

class NotificationService
{
    #[Inject(
        'config.notification.server',
    )]
    public function __construct(
        // $config from local.php (see step 3)
        private readonly array $config
    ) {
    }
    
    // open TCP connection to Dotkernel Queue
    public function createClient(): Socket
    {
        $socketRawFactory = new Factory();
        return $socketRawFactory->createClient(
            $this->config['protocol'] . '://' . $this->config['host'] . ':' . $this->config['port']
        );
    }
    
    // send the PAYLOAD to Dotkernel Queue 
    public function send(string $message): void
    {
        $this->createClient()->write($message . $this->config['eof']);
    }
    
    // encode processed data
    protected function encodeEmailMessage(array $data): string
    {
        return Encoder::encode($data);
    }

    // process and send data
    // change $data type depending on what you need 
    public function sendNewAccountNotification(mixed $data): void
    {
        $data['yourKey'] = $data;
        $this->send($this->encodeEmailMessage($data));
    }
    
    //create your own custom methods to process data
}
```

## Step 2 Install new dependencies

After you have finished creating the new files, navigate to composer.json and add the new dependencies under the require key:

```shell
"ext-sockets": "*",
"clue/socket-raw": "^v1.6.0",
```

Install new dependencies using:

```shell
composer install
```

Under the `autoload` → `psr-4` key add the newly created module:

```shell
"Core\\NotificationSystem\\": "src/Core/src/NotificationSystem/src"
```

Run `composer dump-autoload` to regenerate Composer’s autoload files.

## Step 3 Configuration

Navigate to `config/config.php` and add your new Core ConfigProvider `Core\NotificationSystem\ConfigProvider::class`.

After you have added the new config provider, navigate to `config/autoload/local.php` and add a new config key used for server connection.
> **_NOTE:_**  if you only have the local.php.dist file, duplicate it, and delete .dist from the copy's name.

```shell
'notification' => [
        'server' => [
            'protocol' => 'tcp',
            'host'     => 'IP of the Dotkernel Queue ',
            'port'     => 'port number',
            'eof'      => "\n",
        ],
    ],
```

## Step 4 Service usage

Navigate to your handler, inject the new service and use your custom method where needed.

```shell
    #[Inject(
        NotificationService::class
    )]
    public function __construct(
        protected NotificationService $notificationService
    ) {
```
