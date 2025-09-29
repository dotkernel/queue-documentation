# INSTALLATION

## git clone

```shell
git clone -b default-queue https://github.com/dotkernel/queue.git
```

## Prepare `config/autoload` files

- duplicate `local.php.dist` as `local.php`, then fill in the database credentials and set the `$baseUrl`
- duplicate `log.local.dist` as `log.local`
- duplicate `messenger.local.php.dist` as `messenger.local.php`
- duplicate `swoole.local.php.dist` as `swoole.local.php`

## Run Composer

```shell
composer install --no-dev
```

## Create services (daemon)

- Edit the files from `/daemon` folder and set proper paths
- copy them in /etc/systemd/system/

```shell
sudo cp /home/dotkernel/queue/daemon/swoole.service /etc/systemd/system/swoole.service
```

```shell
sudo cp /home/dotkernel/queue/daemon/messenger.service  /etc/systemd/system/messenger.service
```

## Start the Swoole daemon

```shell
sudo systemctl daemon-reload
```

```shell
sudo systemctl enable swoole.service
```

```shell
sudo systemctl start swoole.service
```

```shell
sudo systemctl status  swoole.service
```

## Start the Messenger daemon

```shell
sudo systemctl daemon-reload
```

```shell
sudo systemctl enable messenger.service
```

```shell
sudo systemctl start messenger.service
```

```shell
sudo systemctl status  messenger.service
```

### Testing the installation

Send a request from your local machine

```shell
echo "Hello" | socat -T1 - TCP:SERVER-IP:8556`
```
