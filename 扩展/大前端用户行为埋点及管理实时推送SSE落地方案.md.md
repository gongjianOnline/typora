# 大前端用户行为埋点及管理端实时推送方案

场景: 在 toC 项目中面向用户会将应用的注册功能暴露给游客,当游客通过注册入口申请某些功能时,需要商务人员第一时间对接到游客信息.

技术点: 服务端发布订阅模式 + SSE推送

技术栈: node Nest.js react

## 服务端

`app.controller.js`

```ts
import { Controller, Get, Sse,Param, Query } from '@nestjs/common';
import { AppService } from './app.service';
import { Observable } from 'rxjs';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  getHello(@Query() data) {
    return this.appService.getHello(data);
  }

  @Sse('/channel')
  sse():Observable<MessageEvent>{
    return this.appService.sseChannel()
  }
}

```

`app.service.ts`

```ts
import { Injectable } from '@nestjs/common';
import { Observable } from 'rxjs';
import * as EventEmitter from 'events';
/*eventHub*/
const myEmitter = new EventEmitter();

@Injectable()
export class AppService {
  getHello(data:any) {
    myEmitter.emit('send', data);
    return {code:200,message:"请求成功"}
  }
    /**sse推送 */
    sseChannel(){
      return new Observable<any>((observer) => {
        myEmitter.on('send', (data: any) => {
          observer.next({ data: data });
        });
      });
    }
}
```

现在测试一下接口

![](https://gjweb.top/wp-content/uploads/2024/04/QQ%E5%BD%95%E5%B1%8F20240421121816-00_00_00-00_00_30-1.gif)

---

## web端

```ts

import React, { useEffect, useMemo } from 'react';
import { Button, Divider, notification, Space } from 'antd';
import type { NotificationArgsProps } from 'antd';




const HomePage:React.FC<any> = ()=>{
  const [api, contextHolder] = notification.useNotification();
  const openNotification = (data:any) => {
    const obj  = JSON.parse(data);
    const key = `open${Date.now()}`;
    const btn = (
      <Space>
        <Button type="link" size="small" onClick={() => api.destroy()}>
          已读
        </Button>
      </Space>
    );
    api.open({
      message: '有新游客',
      description:
        `用户名称：${obj.userName}`,
      btn,
      key,
      onClose: close,
    });
  };

  /*连接后端sse接口*/
  const sse = new EventSource('/sseApi/channel');
  sse.onmessage = function (event) {
    openNotification(event.data)
  }

  return (
    <>
    {contextHolder}
    <Button type="primary" onClick={openNotification}>
      web端实时推送
    </Button>
  </>
  );
}

export default HomePage

```

实现效果

![](https://gjweb.top/wp-content/uploads/2024/04/QQ%E5%BD%95%E5%B1%8F20240421130534-00_00_00-00_00_30.gif)
