# 大前端用户行为埋点及管理端试试推送方案

场景: 在 toC 项目中面向用户会将应用的注册功能暴露给游客,当游客通过注册入口申请某些功能时,需要商务人员第一时间对接到游客信息.

技术点: 服务端发布订阅模式 + SSE推送

技术栈: node Nest.js 

服务端

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

![img](https://gjweb.top/wp-content/uploads/2024/04/01-1024x436.gif)

