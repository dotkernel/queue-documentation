# INSTALLATION

## git clone

```shell
git clone -b default-queue https://github.com/dotkernel/queue.git
``````

## Edit .dist files from config/autoload folder

- local.php.dist
- log.local.dist
- messenger.local.php.dist
- swoole.local.php.dist

## Run Composer

```shell
composer install --no-dev
```

## Create services ( daemon)

- Edit the files from `/daemon` folder and set proper paths
- copy them in /etc/systemd/system/

```shell
sudo cp /home/dotkernel/queue/daemon\swoole.service
```

```shell
sudo cp /home/dotkernel/queue/daemon\messenger.service
```

## Start the daemon

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

### Testing the installation

Send a request from your local machine

```shell
echo "Hello" | socat -T1 - TCP:SERVER-IP:8556`
```
