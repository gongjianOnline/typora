



# Linux常用命令汇总及远程链接

## 一、远程连接Linux

配置虚拟机以 VMware 为例，点击 编辑--虚拟网络编辑器

 ![步骤一](https://s3.bmp.ovh/imgs/2022/02/7e1252d5071a9471.png) 

提示: 下面的主机端口号和虚拟机端口号默认是22 

 ![步骤二](https://s3.bmp.ovh/imgs/2022/02/658ff0d4a92890d8.png) 

以 Ubuntu 系统为例，在终端中执行

```shell
sudo su -
apt-get install openssh-server
ssh localhost
// 在物理机上运行
ssh root@ip
```

运行成功后会看到下面结果

 ![](https://s3.bmp.ovh/imgs/2022/02/2becf3f0f18ccf7c.png) 

### 报错信息  

Permission denied, please try again. 的解决方案

```shell
sudo vi /etc/ssh/sshd_config
```

找到 ermitRootLogin 属性 改成 yes 然后 执行  service sshd restart 

---

## Linux常用命令

### 查看系统信息

| 命令                 | 注释                |
| -------------------- | ------------------- |
| lsd_release -a       | 查看发行版本        |
| uname -a             | 查看内核版本        |
| df                   | 查看 磁盘空间(字节) |
| df -Th               | 查看磁盘空间 (GB)   |
| ls -la               | 查看目录及权限      |
| top                  | 查看所有进程        |
| ps -ef \| grep docer | 搜索 docer 进程     |
| kill -9 [进程号]     | 关闭指定进程        |
| servie sshd status   | 查看 ssh 运行状态   |
| sercie sshd stop     | 关闭 ssh 服务       |
| sercie sshd restart  | 重启服务 ssh        |

### 文档相关命令

| 命令                   | 注释                                                         |
| ---------------------- | ------------------------------------------------------------ |
| ls                     | 查看文件及目录                                               |
| mkdir [文件名]         | 创建目录                                                     |
| touch [文件名]         | 创建文件                                                     |
| vi [文件名]            | 进入文件编辑状态; i 为编辑模式; esc退出编辑模式; :wq保存并退出 ; :q! 不保存并退出 |
| nano [路径\文件名]     | 可对文件进行查看和编辑 , ctrl+x  Y 回车 保存并退出           |
| cat [文件名]           | 查看文件内容                                                 |
| echo "xxx" >> [文件名] | 向文件中追加内容xxx                                          |
| echo "xxx" > [文件名]  | 将文件中的全部文本替换为xxx                                  |
| rm [文件]              | 删除文件                                                     |
| rm -r [目录名]         | 删除目录                                                     |
| rm -rf [文件名]        | 强制删除目录                                                 |

### 下载相关命令

| 命令        | 注释         |
| ----------- | ------------ |
| wget [地址] | 下载指定资源 |

### 解压缩

| 命令                                          | 注释                  |
| --------------------------------------------- | --------------------- |
| tar zxvf [压缩包名称].扩展名                  | 解压 tar.gz 压缩包    |
| unzip [压缩包名称].zip                        | 解压 zip 格式的压缩包 |
| tar zcvf [压缩后的名称].tar.gz [要压缩的目录] |                       |

