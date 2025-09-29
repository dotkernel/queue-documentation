# Server setup

> The below instructions are working only on **AlmaLinux 9** or **10**.
> 
> For other operating systems, they need to be adapted accordingly.
> 
> We recommend to use for development [Hetzner Cloud: € 20 Credit](https://hetzner.cloud/?ref=HYu6z4XGfkcP) 

## Starting point

A server with **AlmaLinux 9** or **10** freshly installed with `root` access.

### Update OS

```shell
dnf update -y
```

### Create a new user with sudo permissions

```shell
useradd dotkernel
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
sudo dnf module enable php:remi-8.4
```

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
