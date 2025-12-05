# conda环境搭建

## linux 安装 conda

将安装包上传到 linux 服务器,并运行命令

```shell
bash Anaconda3-2023.03-Linux-x86_64.sh
```

安装过程中会出现一些提示和选项：

- 阅读许可协议：按`Enter`查看协议内容，或直接按`q`跳过。
- 同意许可协议：输入`yes`并按`Enter`。
- 选择安装路径：默认安装路径通常为`$HOME/anaconda3`，可以直接按`Enter`接受默认路径，或者输入自定义路径。
- 初始化Anaconda：安装完成后会询问是否运行`conda init`来初始化Anaconda环境，输入`yes`并按`Enter`。

安装完成后，需要重新打开终端或运行以下命令来激活Anaconda环境。

```shell
source ~/.bashrc
```

验证安装：

运行`conda`命令来验证Anaconda是否安装成功。

```text
conda --version
```

如果是root用户安装, conda安装包则会在 ` ~/root/` 这个目录里面

### 报错信息

创建 conda python沙盒时报错

这个错误信息非常明确地告诉您：**您没有接受 Conda 默认渠道（Anaconda Channels）的服务条款（Terms of Service, ToS）。**

这个错误是在 Conda 的较新版本中引入的，目的是确保用户在从 Anaconda 官方仓库下载和使用软件包之前，明确了解并接受相关的许可和使用条件。

```shell
conda create -n vllm_env python=3.10 -y
# 报错信息
CondaToSNonInteractiveError: Terms of Service have not been accepted for the following channels. Please accept or remove them before proceeding:
```

#### 解决方法

步骤一：接受服务条款

在 `(base)` 或非环境激活状态下，运行以下两个命令（如果需要）：

```shell
conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main
conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r
```



---

## 安装 jupyter 

安装

```shell
conda install -c conda-forge jupyterlab
```

生成配置文件

```shell
jupyter lab --generate-config
```

启动 (可供外部访问)

```shell
jupyter lab -ip=0.0.0.0 --port=8888 --no-browser
# 如果是 root 用户需要 --allow-root 
jupyter lab --allow-root --ip=0.0.0.0 --port=8888 --no-browser
```

查看 jupyter 是否运行

```shell
jupyter server list
```

卸载 (直接删除安装的目录即可)

```shell
rm -rf ~/root/miniconda3
```

---

