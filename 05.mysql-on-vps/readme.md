# Install MySQL on VPS

## Install MySQL Server

```
apt install default-mysql-server -y
mysql --version
```
## Start and enable the database service

```
systemctl start mariadb
systemctl enable mariadb
systemctl status mariadb
```

