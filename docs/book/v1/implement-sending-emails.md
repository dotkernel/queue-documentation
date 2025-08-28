# IMPLEMENTATION OF EMAIL SENDING SERVICE

To implement such a service, the [send-email](https://github.com/dotkernel/queue/tree/send-email) branch can be taken as a model.

## Step 1 Adding Core

Import the Core from the project from which requests will be sent, in this case the dotkernel admin. This can be done either by directly copying the entire Core folder, or using submodule add if Core is a separate repository.

> **_NOTE:_** make sure you always keep the Core in the queue and the Core in the project it communicates with in sync.

## Step 2 Core dependency

After the Core is added, navigate to the composer.json file and make sure you add all the necessary dependencies for the Core.

```shell
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

Also here under the "autoload" → "psr-4" key make sure you add all the modules from the Core folder.

```shell
"autoload": {
    "psr-4": {
        "Queue\\": "src/",
        "Core\\Admin\\": "src/Core/src/Admin/src",
        "Core\\App\\": "src/Core/src/App/src",
        "Core\\Security\\": "src/Core/src/Security/src",
        "Core\\Setting\\": "src/Core/src/Setting/src",
        "Core\\User\\": "src/Core/src/User/src",
    }
},
```
## Step 3 Install new dependencies

Install all new dependencies using:

```shell
composer install
```

## Step 4 Configuration

Navigate to `config/config.php` and add `ConfigProvider::class` file from all packages you installed.

```shell
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
> **_NOTE:_**  if you only have the local.php.dist file, duplicate it, and delete .dist from the copy's name

```shell
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

Inside your `config/autoload` folder create a new file named `mail.global.php`, copy this [file](https://github.com/dotkernel/queue/blob/send-email/config/autoload/mail.global.php) content and fill in your configuration.

## Step 7 Data management and emails sending

Once everything is installed and configured we can move on to handle the data in the queue. Inside your handler for example `MessageHandler` you can inject all the services you need in order to create and send emails.

In this [file](https://github.com/dotkernel/queue/blob/send-email/src/App/Message/MessageHandler.php) you can follow a simple example of how to create and send an email using data received from the queue inside the handler.
