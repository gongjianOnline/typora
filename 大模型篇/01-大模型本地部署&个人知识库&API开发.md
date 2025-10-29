# 01. 大模型本地部署&个人知识库&API开发

## 模型部署

[Ollama下载地址](https://ollama.com/)

验证 ollama 是否安装成功

```shell
ollama --version
```

在 ollama 官网中 models 挑选需要的模型 (自测: RTX 4060卡 运行 DeepSeek1.5b)

安装模型命令

```shell 
ollama run deepseek-r1:1.5b
```

运行命令同上

```shell
ollama run deepseek-r1:1.5b
```

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/1)

 结束会话

```shell
/bye
```

## 搭建本地知识库 anythingllm

安装 [AnythingLLM | The all-in-one AI application for everyone](https://anythingllm.com/)

安装步骤一路是

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/3)

点击设置选择 **大语言模型**,选择 ollama 和 本地安装的大模型

**![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/4)**

选择 **嵌入器(Embedder)** 文本向量化工具,选择 AnythingLLM Embedder `目前最好用的向量化工具`

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/5)

添加本地知识库

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/6)

上传文件后,将需要文件的选中添加到命名空间的工作区

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/7)

保存后测试当前RAG是否生效

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/8)

## 在线调用API,供开发者进行定制

实际上 anything LLM 本身具备 项目命名空间---RAG项目分配---项目---会话 等业务流程,根据二开可完全支持小型业务系统的办公助手和客服助手的场景; 进入开发者API 申请 密钥

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/10)

点击图片中 **阅读API文档** 查看现有 anything LLM 提供的接口文档

![](https://raw.githubusercontent.com/gongjianOnline/ImgHosting/main/img/11)

 