# nginx 配置 https 环境

以腾讯 ssl 申请为例,从 0 开始配置 https ;

## 腾讯云申请ssl证书

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/1693790916378.png)

申请免费的ssl证书,走完向导,获得证书信息,下载 nginx 证书

证书文件包括

- 域名.csr
- 域名.key
- 域名_bundle.crt
- 域名_bundle.pem

---

## nginx配置

在安装 nginx 默认是没有 https 模块的,需要重新安装

### 查看服务器nginx版本

```shell
./sbin/nginx -V
```

以 1.14.0 为例安装源码包

```shell
wget https://nginx.org/download/nginx-1.14.0.tar.gz 
```

解压

```shell	
unzip nginx-1.14.0.tar.gz 
```

进入 nginx-1.14.0 , 配置

```shell
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
```

执行 

```shell
make
```

备份原有的安装

```shell
cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak
```

编译好的nginx覆盖掉原有的nginx

```shell
cp ./objs/nginx /usr/local/nginx/sbin/
```

将 下载的文件夹 域名_bundle.crt 和 域名.key 两个文件放在 nginx 根目录，在 nginx.config 中添加配置

```nginx
server {
        #SSL 默认访问端口号为 443
        listen 443 ssl; 
        #请填写绑定证书的域名
        server_name 域名; 
        #请填写证书文件的相对路径或绝对路径
        ssl_certificate 域名_bundle.crt; 
        #请填写私钥文件的相对路径或绝对路径
        ssl_certificate_key 域名.key; 
        ssl_session_timeout 5m;
        #请按照以下协议配置
        ssl_protocols TLSv1.2 TLSv1.3; 
        #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE; 
        ssl_prefer_server_ciphers on;
        location / {
            #网站主页路径。此路径仅供参考，具体请您按照实际目录操作。
            #例如，您的网站主页在 Nginx 服务器的 /etc/www 目录下，则请修改 root 后面的 html 为 /etc/www。
            root html; 
            index  index.html index.htm;
        }
    }
```

重启nginx 

```nginx
./sbin/nginx -s reload
```

---

## 配置好后无法通过 https 访问

安装 nmap 

```
yum install nmap
```

使用 nmap 扫描80和443端口

```shell
 nmap -Pn IP -p 80,443
```

如果监听到 443 端口时关闭状态，如下图所示

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/1693805725587.png)

解决方案一：

强制停止nginx 手动启动

```nginx
./sbin/nginx -s stop
./sbin/nginx 
```

重新扫描，查看433是否开启

解决方案二：

如果方案一无效，请检查服务器是否开启状态，可以关闭防火墙

````shell
sudo systemctl stop firewalld  /*停止服务*/
sudo systemctl disable firewalld /*禁用防火墙服务，防止开机自启*/
sudo systemctl status firewalld /*检查防火墙服务状态*/
````

解决方案三：

如果方案一和二都无效可以尝试，将443端口添加到防火墙中

```shell
/*添加一个永久的规则，允许 TCP 连接通过 443 端口。*/
sudo firewall-cmd --zone=public --add-port=443/tcp --permanent
/*重启防火墙*/
sudo firewall-cmd --reload
/*查看端口运行状态*/
sudo firewall-cmd --zone=public --list-ports
```

解决方案四

查看服务器云厂商的安全组时候允许了 443 端口访问;