# linux命令集合

## 目录结构

```
常用目录介绍:
    /   表示 根目录
    bin 存放: 基础命令的
    sbin 存放:超级命令的,例如:权限\配置信息
    root 表示:超级管理员 root 账号,所在目录
    home 表示:游客(其他)账号所在目录
    etc  表示: Linux的配置信息目录
```

## ls基础命令

```
ls [-a -l -h]
    -a 显示隐藏文件/文件夹
    -l 以行的方式显示
    -h 以人性化的方式展示
```

## 文件操作

```
    mkdir 创建目录
    touch 创建文件
    cat 查看文件内容
    cp [-r] 要拷贝的内容 拷贝到的路径  -r表示递归
    mv ..   ...        ... 剪切
    rm [-rf] ... (删除) -r表示递归 -f表示强制
    查找:
        which 命令
            查找命令的程序文件
            语法: which 要查找的命令
            无需选项,只需要参数表示查找哪个命令
        find 命令
            用于查找指定的文件
            按文件名查找: find 开始路径 -name "文件名"
                文件通配符
            按文件大小查找: find 开始路径 -size +|-n[KMG]
                例如: 查找文件中大于 100M 的文件
                    find / -size +100M
```

## grep 管道命令

```
从文件中通过关键字过滤文件行
    语法 : grep [-n] 关键字 关键路径
```

## tail 持续跟踪文件

```
语法: tail 路径
```

## 用户权限

### 查看&创建&切换用户

```
查看全部用户
    getent passwd
创建普通用户
    useradd -m itheima
    passwd itheima
切换用户
    su itheima
```

### 用户组操作

```
查看所有组
    getent group
创建用户组
    groupadd [-g -d] 用户组名
    -g 指定用户的组,不指定 -g , 会创建同组名并自动加入,指定 -g 需要组已经存在,如已存在着同组名,必须使用 -g
    -d 指定用户 HOME 路径,不指定HOME目录默认在: /home/用户名
删除用户组
    groupdel [-r] 用户组名
    -r 删除用户HOME目录
查看用户所属组
    id [用户名]
    不写则查自身
修改用户所属组
    usermod -aG 用户组 用户名
        将指定用户加入到指定用户组中

```

### 修改文件夹权限

```
chmod 修改文件和文件夹权限
    语法: chmod u=rwx,g=rx,o=x 1.txt  将文件权限修改为 rwxr-x--x
        chmod -R u=rwx,g=rx,o=x test 将文件夹 test 以及里面全部内容改成 rwxr-x--x
    语法糖: 0:无权限 4:只读 2:只写 1:可执行
        chmod -R 777 文件名/目录名

chown 修改属主权限
    只能通过root账号修改
    chown [-R] 用户:用户组 文件或文件夹
```

## 下载

```
yum [-y] [install | remove | search] 软件名
    -y 自动确认,无需手动确认安装和卸载过程
```

## 软件启停

```
systemctl 启动 \ 停止 \ 开机自启
    语法: systemctl start | stop | status | enable | disable 服务名
                    启动    关闭   查看状态  开机自启   关机开机自启
```

## 查看&管理进程

```
查看进程
    语法 ps -ef
        -e 显示全部信息
        -f 格式化数据
管理进程
    ps -ef | grep 关键字
    kill [-9] 进程号    关闭指定进程号的进程
```

## 环境变量

```
环境变量
    针对当前用户生效,配置 ~/.bashrc文件中
    针对所用用户生效,配置 /etc/profile文件中
    配置完成后 使用 'source 配置文件' , 进行立刻生效,或重新登录shell生效
```

## 上传下载 lrzsz

```
上传下载命令 rz & sz
    安装: yum -y install lrzsz
```

## 压缩&解压

```
压缩&解压
    压缩 (如果是 gz  -zcvf)
        tar -cvf 压缩包名称.格式[zip | gz | tar]  文件1 文件2 ....
    解压 (如果是 gz  -zxvf)
        tar -xvf 压缩包.格式 -C 指定路径
```

