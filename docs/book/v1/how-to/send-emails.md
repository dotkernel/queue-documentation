# SEND EMAILS

Using a queuing service solves problems such as server overload. For example, if a server receives a large number of requests, it tries to process them synchronously, resulting in long response times or even server crashes.

A concrete example is sending emails. While a series of tasks are running on the server, a task such as sending an email is passed to a queue and run in the background so the server can move on to the next task, while the queue composes the email and sends it. Tasks are queued and processed gradually (FIFO), depending on available resources.

To implement such a service, the [`send-email`](https://github.com/dotkernel/queue/tree/send-email) branch can be taken as a model.

> **_NOTE:_**  The default branch 1.0 holds only the base code of Queue and provides essential features such as:
>
> * Adding messages to the queue
> * Retrieving and processing messages (FIFO)

## Step 1 Importing Core

Importing the Core is essential for the queue to communicate with the main application logic (for this case [`dotkernel admin`](https://github.com/dotkernel/admin)). Whether you copy the Core folder directly or add it as a submodule, it’s important to keep the Core in sync between the main project and the queue so that all classes, services, and configurations are available for message processing.

## Step 2 Core dependency

After adding the Core, you need to make sure all external dependencies are included in composer.json. Without them, Core functionalities (cache, mail, authentication, etc.) won’t work in the queue.

```php
"dotkernel/dot-cache": "^4.3",
"dotkernel/dot-data-fixtures": "^1.4.0",
"dotkernel/dot-errorhandler": "4.2.1",
"dotkernel/dot-mail": "^5.3.0",
"laminas/laminas-authentication": "2.18.0",
"mezzio/mezzio-authentication-oauth2": "^2.11",
"mezzio/mezzio-twigrenderer": "^2.17.0",
"ramsey/uuid": "^4.5.0",
"ramsey/uuid-doctrine": "^2.1.0",
"roave/psr-container-doctrine": "^5.2.2",
```

Adding Core modules under `autoload` → `psr-4` enables automatic class loading, so you don’t have to manually require each class.

```php
"autoload": {
    "psr-4": {
        "Queue\\": "src/",
        "Core\\Admin\\": "src/Core/src/Admin/src",
        "Core\\App\\": "src/Core/src/App/src",
        "Core\\Security\\": "src/Core/src/Security/src",
        "Core\\Setting\\": "src/Core/src/Setting/src",
        "Core\\User\\": "src/Core/src/User/src",
    }
}
```

## Step 3 Install new dependencies

Install all new dependencies using:

```shell
composer install
```

Running `composer install` ensures that all packages required for Core and the queue are downloaded. This includes third-party libraries and all project-specific dependencies.

## Step 4 Configuration

Navigate to `config/config.php` and add `ConfigProvider::class` file from all packages you installed.

```php
Mezzio\Twig\ConfigProvider::class,
Dot\Cache\ConfigProvider::class,
Dot\DataFixtures\ConfigProvider::class,
Dot\Mail\ConfigProvider::class,

// Core
Core\Admin\ConfigProvider::class,
Core\App\ConfigProvider::class,
Core\Security\ConfigProvider::class,
Core\Setting\ConfigProvider::class,
Core\User\ConfigProvider::class,
```

## Step 5 Database connection

Navigate to `config/autoload/local.php` and fill in the database connection details.
> **_NOTE:_**  if you only have the local.php.dist file, duplicate it, and delete .dist from the copy's name.

```php
$databases = [
    'default' => [
        'host'     => '',
        'dbname'   => '',
        'user'     => '',
        'password' => '',
        'port'     => 3306,
        'driver'   => 'pdo_mysql',
        'charset'  => 'utf8mb4',
        'collate'  => 'utf8mb4_general_ci',
    ],
];
```

## Step 6 Email configuration

Inside your `config/autoload` folder create a new file named `mail.global.php`, copy this [file](https://github.com/dotkernel/queue/blob/send-email/config/autoload/mail.global.php) content and fill in your configuration. The queue will use these settings to send emails in the background.

## Step 7 Data management and emails sending

Once everything is installed and configured we can move on to handle the data in the queue. In the message handler for example `MessageHandler`, each message from the queue is processed, the email is composed, and then sent. By injecting the required services and using templates, the handler can send emails without blocking the main application, respecting FIFO and asynchronous processing.

In this [file](https://github.com/dotkernel/queue/blob/send-email/src/App/Message/MessageHandler.php) you can follow a simple example of how to create and send an email using data received from the queue inside the handler.
