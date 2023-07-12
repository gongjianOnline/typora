# centOS上安装nginx

更新系统软件包列表

```shell
yum update
```

安装 nginx 安装包

```shell
yum install nginx
```

启动 nginx 命令

```sehll
systemctl start nginx
```

验证是否重启成功

```shell
systemctl status nginx
```

如果状态显示为 **active(running)** 则表示 nginx 已经成功启动

配置防火墙规则，允许 HTTP 和 HTTPS 流量访问 nginx ， 默认情况下 CentOS 的防火墙可能会阻止这些流量。 可以使用以下命令开启 HTTP 和 HTTPS 访问

```shell 
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```

查看安装目录

```shell
whereis nginx
/usr/sbin/nginx /usr/lib64/nginx /etc/nginx /usr/share/nginx 
```

**/etc/nginx** 路径中可以配置 **nginx.conf**  文件

**/usr/share/nginx** 路径的 **html** 文件夹前端内容

## 报错提示

报错一：FirewallD is not running

启动 FirewallD 服务 

```shell
systemctl start firewalld
```

确认 FireWallD 是否已成功启动

```shell
systemctl status firewalld
```

如果状态为 **active(running)** ，表示启动成功，成功后重新配置防火墙规则

```shell
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```

