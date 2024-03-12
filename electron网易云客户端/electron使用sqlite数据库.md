# electron 使用本地化数据库 sqlite 

## 安装

```shell
cnpm install sqlite3 knex
```

注意在使用 yarn 和 pnpm 安装 **sqlite3** 时会安装失败，考虑是镜像问题使用 nrm 转换成了 淘宝镜像，再一次安装后仍然失败，npm的淘宝镜像也存在上述问题，最后使用 cnpm 成功安装了 sqlite3。 但全局的node_module 发生了混乱，导致全部依赖都检索失败，最终删除了 node_module 和 locak 文件，使用 cnpm 重新构建 node_module

**建议使用 yarn 安装 sqlite3@5.1.6，5.1.7 只能使用cnpm安装，但根据cnpm管理器的特点无法扁平化管理依赖文件，打包运行会出现莫名的问题**

---

## 初始化数据库和表结构

创建 sql 目录 createDB.ts

```ts
import { app } from 'electron';
import knex from "knex";
import {join} from 'path';

const db = knex({
  client: 'sqlite3',
  connection: {
    filename:join(app.getPath('userData'), 'renderFarm.sqlite') // 本地数据库文件路径
  },
  useNullAsDefault: true // 在 SQLite 中使用 NULL 作为默认值
})
/**初始化数据库创建相关表 */
export const initDatabase = async () => {
  try {
    // 创建你的表或者其他初始化操作
    const userList = await db.schema.hasTable('userList');
    if(!jobListTableExists){
      await db.schema.createTable('jobList', table => { /**工作表 */
        table.string('id').primary(); /**主键，id不自增 */
        table.string('userName'); /**用户名 */
        table.integer('userId'); /**用户id */
      });
    }
    const configTableExists = await db.schema.hasTable('config');
    if(!configTableExists){
      await db.schema.createTable("config",table=>{ /**软件配置表,用于存储个软件版本及环境的安装状态 */
        table.increments('id'); /*递增主键*/
        table.string('softwareName'); /**软件及环境名称 */
        table.integer("installStatus");/**安装状态 */
      })
    }
    console.log('sqliteDB 表创建完成');
  } catch (error) {
    console.error('sqliteDB 表创建失败', error);
  }
};

/**断开sqlite连接 */
export const closeDatabase = async () => {
  try {
    await db.destroy();
    console.log('sqlite已断开');
  } catch (error) {
    console.error('sqlite断开时出错', error);
  }
};

/**增加数据 */
/**示例：db('your_table_name').insert({
  username: 'ccc',
  userId: 123,
  email: 'xxx',
  date: Date.now()
})*/
interface ConfigType{
  softwareName:string,
  installStatus:number
}
export const addSQData = async (tableName,data:JobListDataType | ConfigType)=>{
  try {
    let result = await db(tableName).insert({
      ...data
    })
    // console.log("添加成功",result)
    return {code:101,data:result}
  } catch (error) {
    // console.log("添加失败",error)
    return {code:102,data:error}
  }
}

/**查询数据*/
/**db('your_table_name').select('*').where({ username: 'john_doe' }) */
export const querySQData = async (tableName,query)=>{
  try {
    let result = await db(tableName).select('*').where({...query})
    // console.log("查询成功",result)
    return {code:101,data:result}
  }catch(error){
    // console.log("查询失败",error)
    return {code:102,data:null}
  }
}

/**复杂查询 */
/**db('your_table_name').select('*').where({ username: 'john_doe' }).whereIn('字段',[参数1,参数2]) */
export const complexQuerySQData = async (tableName,query,complexquery)=>{
  try {
    let result = await db(tableName).select('*').where({...query}).whereIn(complexquery[0],complexquery[1])
    return {code:101,data:result}
  } catch (error) {
    return {code:102,data:error}
  }
}

/**删除数据 */
/**db('your_table_name').where({ username: 'john_doe' }).del() */
export const deleteSQData = async (tableName,query)=>{
  try {
    let result = await db(tableName).where({...query}).del();
    // console.log("删除成功",result)
    return {code:101,data:result}
  } catch (error) {
    // console.log("删除失败",error)
    return {code:102,data:error}
  }
}

/**修改数据 */
export const modifySQData = async (tableName="jobList",query,data)=>{
  try {
    console.log(query,data)
    let result = await db(tableName).where({...query}).update({...data})
    // console.log("修改成功",result)
    return {code:101,data:result}
  } catch (error) {
    // console.log("修改失败",error)
    return {code:102,data:error}
  }
}
```

将该文件引入到 electron 初始化文件中 index.ts

```.ts
import {initDatabase} from "./sql/createDB"
app.whenReady().then(async () => {
	  await initDatabase()
})
```

**如果是在渲染层调用相关数据库操作，可封装一个IPC通信文件**

---

## 渲染层操作数据库封装 IPC 通信文件

创建 SQIPC.ts

```ts
import {ipcMain} from "electron"
import {
  addSQData,
  querySQData,
  deleteSQData,
  modifySQData,
  complexQuerySQData
} from "./createDB"

/**新增数据*/
interface SQLParams {
  tableName:string
  data:any
  newData?:any
  [propsName:string]:any
}
ipcMain.handle('addSQLData', async (_,value:SQLParams) => {
  return addSQData(
    value.tableName,
    value.data
  )
})

/**查找数据 */
ipcMain.handle('querySQLData', async (_,value:SQLParams) => {
  return querySQData(
    value.tableName,
    value.data
  )
})

/**删除数据 */
ipcMain.handle('deleteSQData', async (_,value:SQLParams) => {
  return deleteSQData(value.tableName,value.data)
})

/**修改数据 */
ipcMain.handle('modifySQData', async (_,value:SQLParams) => {
  return modifySQData(value.tableName,value.data,value.newData)
})

/**复杂查找 */
ipcMain.handle('complexQuerySQData', async (_,value) => {
  return complexQuerySQData(value.tableName,value.data,value.complexQuery)
})
```

在主进程 index.ts 中引用一下

```ts
import "./sql/SQIPC.ts"
```

在中间层 preload 中注册 

```ts
/**sqlite通信接口 */
interface SQLParams {
  tableName:string
  data:any
  complexQuery:any[]
}
const electronSQ = {
  "addSQLData":(value:SQLParams)=>{
    console.log("中间层",value)
    return ipcRenderer.invoke("addSQLData",value)
  },
  "querySQLData":(value:SQLParams)=>{
    return ipcRenderer.invoke("querySQLData",value)
  },
  "deleteSQData":(value:SQLParams)=>{
    return ipcRenderer.invoke("deleteSQData",value)
  },
  "modifySQData":(value:SQLParams)=>{
    return ipcRenderer.invoke("modifySQData",value)
  },
  "complexQuerySQData":(value:SQLParams)=>{
    return ipcRenderer.invoke("complexQuerySQData",value)
  }
}

if(){
    try{
        contextBridge.exposeInMainWorld('electronSQ', electronSQ)
    }catch(){
        console.error(error)
    }
}
```



