# liunx安装docker&docker-compose

## 安装 docker

卸载旧版docker

```shell
sudo apt-get remove docker docker-engine docker-ce docker.io
```

 更新apt包索引

```shell
sudo apt-get update
```

 执行下列命令行，使apt可以通过HTTPS协议去使用存储库 

```shell
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
```

 添加Docker官方提供的GPG密钥

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

 设置stable存储库 

```shell
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

 安装最新版本的docker-ce 

```shell
sudo apt-get install -y docker-ce
```

修改docker为国内镜像, 在 /etc/docker/daemon.json 添加如下代码

```shell
{
	"registry-mirrors": ["https://mirror.ccs.tencentyun.com","http://hub-mirror.c.163.com"]
}

```

运行一个 hello-world

```shell
sudo docker run hello-world
```

---

## 安装docker-compose

```shell
curl -L https://get.daocloud.io/docker/compose/releases/download/1.29.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

---

## docker常用命令

| 命令                    | 注释           |
| ----------------------- | -------------- |
| docker rm [容器名]      | 删除容器       |
| docker ps -a            | 查看所有容器   |
| docker ps               | 查看运行的容器 |
| docker stop [容器名]    | 停止运行的容器 |
| docker logs -f [容器名] | 持续输出日志   |

