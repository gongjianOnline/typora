# docker运行相关环境

## 安装并运行 DOClever 

```shell
sudo su - // 切换到管理员用户
cd /home/
mkdir doclever
cd doclever
vi docker-compose.yml
```

在 docker-compose.yml 中插入

```yaml
version: "2"
services:
  DOClever:
    image: lw96/doclever
    restart: always
    container_name: "DOClever"
    ports:
    - 20080:10000 // 运行到 20080端口上 (注释代码不要复制到liunx上)
    volumes:
    - /srv/doclever/file:/root/DOClever/data/file
    - /srv/doclever/img:/root/DOClever/data/img
    - /srv/doclever/tmp:/root/DOClever/data/tmp
    environment:
    - DB_HOST=mongodb://mongo:27017/DOClever
    - PORT=10000
    links:
    - mongo:mongo

  mongo:
    image: mongo:latest
    restart: always
    container_name: "mongodb"
    volumes:
    - /srv/doclever/db:/data/db
```

