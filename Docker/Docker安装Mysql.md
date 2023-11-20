# 01. docker 搭建 MySql

## 拉去 MySql

```shell
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=root用户设置的密码 -d mysql
```

## 配置 docker-compose.yml 文件

```yml
# Use root/example as user/password credentials
version: '3.1'

services:

  db:
    image: mysql
    # NOTE: use of "mysql_native_password" is not recommended: https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html#upgrade-caching-sha2-password
    # (this is just an example, not intended to be a production configuration)
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
    ports:
      - 3090:3306

  adminer:
    image: adminer
    restart: always
    ports:
      - 8090:8080
```

本地通过 8090端口访问 adminer 数据库管理页面，通过 3090 端口访问docker mysql 数据库；

运行命令

```shell
docker-compose up -d;
```

## 运行命令

`docker ps` 查看运行内容容器

`docker stop xxx` 停止运行

`docker rm xxx` 删除容器

`docker --version` 查看 docker 版本

`docker-compose` 查看 docker-compose 版本