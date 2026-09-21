# Server setup

## Summary

Step-by-step instructions for provisioning a fresh AlmaLinux 9/10 server with the
users, PHP runtime, Swoole and Redis/Valkey extensions, and firewall rules the queue
daemon needs.

## Details

The below instructions were tested only on **AlmaLinux 9** or **10**.

*For other operating systems, they need to be adapted accordingly.*

> We recommend the use of [Hetzner Cloud via our referral link](https://hetzner.cloud/?ref=HYu6z4XGfkcP) for development thanks to the initial € 20 free [Hetzner Cloud credit](https://www.hetzner.com/legal/referrals).

## Starting point

A server with **AlmaLinux 9** or **10** freshly installed with `root` access.

### Update OS

```shell
dnf update -y
```

### Create a new user with sudo permissions

```shell
useradd dotkernel
useradd --system --no-create-home queue
```

```shell
passwd dotkernel
```

```shell
usermod -aG wheel dotkernel
```

> Reboot server.

> SSH into the server as `dotkernel`.

### Install various utilities

```shell
sudo dnf install -y dnf-utils
```

```shell
sudo dnf install zip unzip socat wget
```

### PHP

```shell
sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-$(rpm -E %almalinux).rpm
```

```shell
sudo dnf module enable php:remi-8.5
```

> PHP 8.4 (`php:remi-8.4`) is also supported (`composer.json` allows
> `~8.4.0 || ~8.5.0`); substitute the module version above if you need 8.4.

```shell
sudo dnf install -y php php-cli php-common php-intl
```

### Start PHP-FPM

```shell
sudo systemctl start php-fpm
```

```shell
sudo systemctl enable php-fpm
```

### Install and verify swoole

```shell
sudo dnf install php-pecl-swoole6
```

```shell
php -i | grep swoole
```

### Valkey

```shell
sudo dnf install valkey
```

```shell
sudo systemctl enable valkey
```

```shell
sudo systemctl start valkey
```

```shell
sudo valkey-cli ping
```

### Valkey PHP module

```shell
sudo dnf install php-pecl-redis
```

Make sure that the module is installed.

```shell
php -i | grep redis
```

### Git

```shell
sudo dnf install git
```

### Composer

```shell
sudo wget https://getcomposer.org/installer -O composer-installer.php
```

```shell
sudo chmod 777 /usr/local/bin
```

```shell
sudo php composer-installer.php --filename=composer --install-dir=/usr/local/bin
```

### Firewall setup

To add a minimum level of security, a firewall needs to be installed and allow connections from outside only to certain ports, from certain IPs.

> Firewall setup is not mandatory

```shell
sudo dnf install firewalld
```

```shell
sudo systemctl enable firewalld
```

> Before starting the firewall, make sure you will not get locked outside:

```shell
sudo firewall-offline-cmd --zone=public --add-port=22/tcp --permanent
```

Then enable the firewall:

```shell
sudo systemctl start firewalld
```

> By default, Swoole runs on port **8556**.
> You can change that in the configuration file.

```shell
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="YOUR_IP_ADDRESS" port port="8556" protocol="tcp" accept'
```

```shell
sudo firewall-cmd --reload
```

> NOW THE SERVER IS READY

## FAQ

**Q: Which operating systems does this guide support?**

A: It was tested on AlmaLinux 9 and 10; other operating systems need the steps
adapted accordingly.

**Q: Which PHP versions can I install?**

A: PHP 8.5 (`php:remi-8.5`) is documented here, and PHP 8.4 (`php:remi-8.4`) is also
supported since `composer.json` allows `~8.4.0 || ~8.5.0`.

**Q: Which system users does the queue need?**

A: A sudo-capable `dotkernel` user for administration, and a `queue` system
user/group, which is what the shipped `swoole.service` and `messenger.service` unit
files run as.

**Q: Is the firewall setup mandatory?**

A: No, but it's recommended — it restricts inbound connections on the queue's TCP
port (8556 by default) to specific source IPs.
