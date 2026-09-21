# INSTALLATION

## Summary

How to get a working copy of the queue running on the server prepared in
[Server setup](server-setup.md): clone the repository, configure
`config/autoload`, install dependencies, register the systemd daemons, and confirm
the listener responds.

## Location

- Because you are logged in now with the non-root user `dotkernel`, your current server path must be `/home/dotkernel`

> Please be sure you are using the non-root user for the below installation

## git clone

```shell
git clone https://github.com/dotkernel/queue.git
```

> The installation path should be now `/home/dotkernel/queue`

## Prepare `config/autoload` files

- duplicate `local.php.dist` as `local.php`, then fill in the database credentials and set the `$baseUrl`
- duplicate `log.local.php.dist` as `log.local.php`
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
sudo systemctl status swoole.service
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
sudo systemctl status messenger.service
```

### Testing the installation

Send a request from your local machine

```shell
echo "Hello" | socat -T1 - TCP:SERVER-IP:8556
```

> **_NOTE:_**  Any message that is not one of `failed`, `processed` or `inventory` is
> queued twice by design: once with your payload, and once more with the literal
> payload `with 5 seconds delay`, queued 5 seconds later. Expect two entries in
> `inventory`/the logs for every test message you send.

## FAQ

**Q: Which branch should I clone?**

A: Clone without specifying `-b`; this checks out the repository's default branch
instead of pinning to a branch name that can go stale.

**Q: Why does copying `log.local.php.dist` correctly matter?**

A: `config/config.php` only loads local config files that end in `.php`; if the copy
is misnamed the logger silently never loads.

**Q: What should I see after the smoke test?**

A: Two entries appear for the single `echo "Hello"` message you sent — your message,
plus a second, hardcoded `with 5 seconds delay` message queued automatically 5
seconds later.
