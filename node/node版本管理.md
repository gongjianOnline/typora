

# Node多版本管理工具

使用nvm 在 Windows 计算机上管理 node.js 的多个安装 , [前往下载](https://github.com/coreybutler/nvm-windows/releases)

## window安装填坑

**不要** 将 nvm **安装到 C盘** !!  , 如果安装到C盘切换版本的时候会出现 **"拒绝访问"** 的报错信息

安装完成后需要管理员身份运行 porwerShell 进行一下命令 

---

## nvm 命令

- **`nvm arch [32|64]`**：显示节点是在 32 位还是 64 位模式下运行。指定 32 或 64 以覆盖默认体系结构。
- **`nvm current`**: 显示活动版本。
- **`nvm install  [arch]`**：版本可以是特定版本，“latest”表示最新的当前版本，“lts”表示最新的 LTS 版本。（可选）指定是安装 32 位还是 64 位版本（默认为系统架构）。将 [arch] 设置为“all”以安装 32 位和 64 位版本。添加`--insecure`到此命令的末尾以绕过远程下载服务器的 SSL 验证。
- **`nvm list [available]`**：列出 node.js 安装。`available`在末尾键入以显示可供下载的版本列表。
- **`nvm on`**: 启用 node.js 版本管理。
- **`nvm off`**：禁用 node.js 版本管理（不卸载任何东西）。
- **`nvm proxy [url]`**：设置用于下载的代理。留`[url]`空白，以查看当前的代理。设置`[url]`为“none”以删除代理。
- **`nvm uninstall `**: 卸载特定版本。
- **`nvm use  [arch]`**: 切换到使用指定的版本。有选择地使用`latest`，`lts`或`newest`。`newest`是最新*安装的*版本。（可选）指定 32/64 位架构。`nvm use `将继续使用所选版本，但切换到 32/64 位模式。有关`use`在特定目录中使用（或使用`.nvmrc`）的信息，请参阅[问题 #16](https://github.com/coreybutler/nvm-windows/issues/16)。
- **`nvm root `**: 设置nvm存放不同版本node.js的目录。如果``未设置，将显示当前根。
- ** `nvm version`：显示当前运行的 NVM for Windows 版本。
- **`nvm node_mirror `**: 设置节点镜像。中国人可以使用*https://npm.taobao.org/mirrors/node/*
- **`nvm npm_mirror `**: 设置npm镜像。中国人可以使用*https://npm.taobao.org/mirrors/npm/*

