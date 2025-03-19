# 服务端渲染Nuxt3部署方案

## 场景描述

线上服务器系统是 centos7.9 版本，系统最高只能安装 <= 16 版本的 node，在使用 Nuxt3 中至少需要18以上版本的node进行运行。参考现有网上资料都是升级系统底层插件来支持更高版本的 node 环境，这种方式对系统的破环性较高，且尝试后并不生效，下面介绍成本更优的方式，采用 docker 进行容器部署运行，相关 docker 的基本概念和操作请自行 GPT 了解。 

## docker部署方案

安装 docker

```do
# 移除旧版本docker
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
# 配置docker yum源。
sudo yum install -y yum-utils
sudo yum-config-manager \
--add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 安装 最新 docker
sudo yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 启动& 开机启动docker； enable + start 二合一
systemctl enable docker --now

# 配置加速
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
    	"https://crszjgfd.mirror.aliyuncs.com",
    	"https://registry.cn-hangzhou.aliyuncs.com",
    	"https://docker.unsee.tech",
    	"https://mirror.ccs.tencentyun.com",
    	"https://docker.mirrors.ustc.edu.cn",
    	"https://docker.m.daocloud.io",
    	"https://noohub.ru",
    	"https://huecker.io",
    	"https://dockerhub.timeweb.cloud"
    ],
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

在项目中创建 Docker-compose.yml 文件

```yml
version: '3.8'  // 指定 docker-compose 适用于的 docker 版本
services: // 定义 docker-compose 管理服务，可理解为多个 docker 容器配置
  nuxt-app: // Nuxt3应用的服务名称，可随意命名，这个名称在 docker-compose 运行时会作为文件的别名
    build: . // 指定 docker 镜像的构建路径，这里指向当前目录，docker-compose 会查找 Dockerfile 文件并构建镜像
    ports: // 端口映射【宿主机端口:容器端口】
      - "3000:3000"
    restart: always // 重启策略：always 任何情况下容器都会自动重启 no 不自动重启 unless-stopped 重启自动重启，除非手动停止 on-failure 容器只有在发生错误退出时才会重启

```

在项目中创建 Dockerfile 文件

```dockerfile
FROM node:20.18-alpine

# 设置工作目录
WORKDIR /app

# 复制项目文件
COPY . .

# 设置npm镜像源
RUN npm config set registry https://registry.npmmirror.com/

# 安装依赖
RUN npm install

# 构建项目
RUN npm run build

# 暴露端口
EXPOSE 3000

# 启动应用
CMD ["node", "./.output/server/index.mjs"]

# 自动重启策略
# 在docker run时使用 --restart always 参数
```

将全部项目文件上传到服务器运行 docker 命令
```shell
docker compose up -d // 这里使用的是 docker-compose-plugin 
// 如果使用的旧版本的 docker-compose 执行下面命令
docker-compose up -d 
```



