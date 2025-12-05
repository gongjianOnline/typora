# windows 创建 WSL 子系统

当想在 windows 中使用 linux 系统，有避免做双系统的时候，可使用 win 10 以上版本提供 WSL 子系统模块，快速构建 linux 环境；并且可无缝调用物理机中的 GPU 和 CPU ；

## 安装 WSl2

以打开 PowerShell 管理员身份

```shell
wsl --install
```

此命令会自动执行以下操作：

- 启用必需的 **“适用于 Linux 的 Windows 子系统”** 和 **“虚拟机平台”** 可选组件。
- 下载并安装最新的 **Linux 内核**。
- 下载并安装默认的 **Ubuntu** Linux 发行版。
- 自动将安装的发行版设置为使用 **WSL2** 架构。

安装完成后 重启窗口，进入 unbntu 系统

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/Snipaste_2025-12-05_13-27-20.png)

---

## WSL子系统 CUDA安装

CUDA 官网 [CUDA 工具包 - 免费工具和培训 | NVIDIA 开发者](https://developer.nvidia.cn/cuda-toolkit)

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/4047541a-55cc-45f7-bc61-8ade4c48bdd0.png)

在 WSL2 的 Ubuntu 环境中，通常推荐选择 **`deb` (Debian Package)** 类型。这是因为 `deb` 格式是基于 Debian 的 Linux 发行版（如 Ubuntu）的标准安装包格式，最容易安装和管理。

运行下方命令

```shell
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/13.1.0/local_installers/cuda-repo-wsl-ubuntu-13-1-local_13.1.0-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-13-1-local_13.1.0-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-13-1-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-13-1
```

验证是否安装成功

```hell
nvcc --version
```

### 错误处理

```shell
nvcc --version
Command 'nvcc' not found, but can be installed with:
sudo apt install nvidia-cuda-toolkit
```

已经安装了 CUDA 驱动库，但是系统找不到 `nvcc` 这个命令，它建议安装一个包含该命令的软件包。

#### 步骤一：查找 CUDA 的安装路径

在 Linux 系统中，CUDA Toolkit 通常安装在 `/usr/local/` 目录下。需要找到安装的版本的 `bin` 目录。

运行以下命令来确认路径：

```
ls /usr/local/
```

#### 步骤二：手动设置环境变量

假设CUDA 安装目录是 `/usr/local/cuda-13.1`（或者 `/usr/local/cuda`，两者通常是链接关系），需要将它的 `bin` 目录添加到 `$PATH`。

1. **打开shell 配置文件：** 在 WSL2 的 Ubuntu 中，通常是 `.bashrc`。

   ```
   nano ~/.bashrc
   ```

2. **在文件末尾添加以下两行：** （请将 `cuda-13.1` 替换为实际看到的目录名，或者直接使用 `/usr/local/cuda`，因为它通常是指向最新安装版本的软链接。）

   Bash

   ```
   export PATH=/usr/local/cuda/bin:$PATH
   export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
   ```

3. **保存并退出：**

   - 按 `Ctrl + O` (写入文件)。
   - 按 `Enter` (确认文件名)。
   - 按 `Ctrl + X` (退出 `nano` 编辑器)。

4. **使更改生效**
   ```
   source ~/.bashrc
   ```
   
   重新验证,如下提示则为成功
   
   
   
   



## 验证  wsl 子系统中是否可以读取到 GPU

```python
nvidia-smi
```

