# webpack打包SSR

## 客户端渲染  VS  服务端渲染

|          | 客户端渲染                                  | 服务端渲染            |
| -------- | ------------------------------------------- | --------------------- |
| 请求     | 多个请求(HTML\CSS\JS\Data)                  | 1个请求               |
| 加载过程 | HTML&数据串行加载                           | 1个请求返回HTML和数据 |
| 渲染     | 前端渲染                                    | 服务端渲染            |
| 可交互   | 图片等静态资源加载完成,JS逻辑执行完成可交互 | 同客户端一致          |

