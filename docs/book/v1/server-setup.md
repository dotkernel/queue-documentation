# Server setup

> Below instructions are working only on **AlmaLinux 9**
>
>For other OS's need to be adapted accordingly

## Starting point

A server with **AlmaLinux 9** freshly installed, with root access and updated

### Update OS

```shell
dnf update
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

### reboot

### SSH to the server as new user

### Install various utilities

```shell
sudo dnf install -y dnf-utils
```

```shell
sudo dnf install zip unzip socat wget
```

### PHP

```shell
sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
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

```shell
php -i | grep redis
```

### GIT

```shell
sudo dnf install git
```

### Composer

```shell
wget https://getcomposer.org/installer -O composer-installer.php
```

```shell
sudo chmod 777 /usr/local/bin
```

```shell
php composer-installer.php --filename=composer --install-dir=/usr/local/bin
```

### Firewall setup

In order to add a minimum level of security, a firewall needs to be installed and allows  
connections from outside only to certain ports, from certain IP.

> Firewall setup is not mandatory

```shell
sudo dnf install firewalld
```

```shell
sudo systemctl enable firewalld
```

> Before starting the firewall, be sure you will not be locked outside

```shell
sudo firewall-offline-cmd --zone=public --add-port=22/tcp --permanent
```

```shell
sudo systemctl start firewalld
```

> By default, Swoole runs on port 8556. You can change that in the configuration file.

```shell
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="YOUR_IP_ADDRESS" port port="8556" protocol="tcp" accept'
```

```shell
sudo firewall-cmd --reload
```

### NOW THE SERVER IS READY
