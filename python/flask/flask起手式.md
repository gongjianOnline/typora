# flask起手式

## 创建flask项目

可通过 pychorm 直接创建

```file
|--projectName
	|-- .venv
	|-- database 数据库连接
		|-- db.py
	|-- decorator 装饰器封装
		|-- token.py 用于token验证拦截
	|-- routes 路由文件
		|-- *.py 用于存放所有业务API
	|-- static 静态资源文件
	|-- templates 前端模板文件（前后端分离可忽略）
	|-- main.py 主程序入口
	|-- package.txt 等同于node标准的package.json 用于存放项目依赖版本
```

- 在python中 package.txt 通常被叫做 requirements.txt 

---

## 生成依赖文件以及下载项目依赖

生成依赖文件

```shell
pip freeze > package.txt
```

下载项目中的依赖

```shell
pip install -r package.txt
```

---

## 通过蓝图概念做接口模块拆分

常见一个 api.py 文件

```
# api.py
from flask import Blueprint, jsonify

api_bp = Blueprint("api", __name__)

@api_bp.route('/apiTest', methods=['GET'])
def apiTest():
	return jsonify({"code":200,"data":[]})
```

在 main.py 注册接口API

```python
from flask import Flask
from routes.api_bp import api_bp

app = Flask(__name__)

# 注册蓝图
app.register_blueprint(api_bp)

if __name__ == '__main__':
    app.run(host='127.0.0.1', port=3000)
```

需要用命令行启动服务

```shell
python main.py
```

---

## RESTful API 构建

查询字符串方式

```python
from flask import Blueprint, request, jsonify

user_bp = Blueprint("user", __name__)

@user_bp.route('/getUserinfo', methods=['GET'])
def get_userinfo():
    age = request.args.get('age') # 查询字符串方式接惨
    print("index", age)
    return jsonify({"code": 200, "data": []})
```

formData 方式

```python
from flask import Blueprint, request, jsonify

auth_bp = Blueprint("auth", __name__)

@auth_bp.route('/login', methods=['POST'])
def login():
    user = request.form.get('user')
    print('user', user)
    return jsonify({"code": 200, "message": "请求成功"})
```

json 方式

```python
from flask import Blueprint, request

admin_bp = Blueprint("admin", __name__)

@admin_bp.route("/userList", methods=['POST'])
def user_list():
    data = request.json
    print(data)
    return jsonify({"code": 200, "message": "请求成功"})

```

上传文件



sse推送

```python
from flask import Flask, Response, Blueprint
import time

sse_bp = Blueprint("sse", __name__)

@sse_bp.route("/stream")
def stream():
    def event_stream():
        while True:
            # SSE 格式：必须以 "data:" 开头，以两个换行结尾
            yield f"data: 服务器时间 {time.strftime('%H:%M:%S')}\n\n"
            time.sleep(1)  # 每 1 秒推送一次
    return Response(event_stream(), mimetype="text/event-stream")
```

---

## 数据库连接

### 方式一：

使用 pymysql + dbutils 连接池的方式, 缺点：无法使用ORM架构体系，全部查询基于原生SQL

```shell
pip install pymysql dbutils
```

在 database  / db.py 中创建连接池

```
# db.py
import pymysql
from dbutils.pooled_db import PooledDB
from pymysql.cursors import DictCursor

pool = PooledDB(
    creator=pymysql,
    maxconnections=20,     # 连接池最大连接数
    mincached=2,           # 启动时创建的空闲连接
    maxcached=10,          # 连接池最多空闲连接数
    blocking=True,         # 池满后阻塞等待
    ping=5,                # 0=从不，1=默认:每次请求前ping，避免MySQL断开
    host="127.0.0.1",
    port=3307,
    user="root",
    password="",
    database="vote",
    charset="utf8mb4",
    cursorclass=DictCursor # 返回字典
)

def fetch_all(sql):
    conn = pool.connection()
    cursor = conn.cursor()
    cursor.execute(sql)
    result = cursor.fetchall()
    cursor.close()
    conn.close()
    return result
```

使用

```python
from flask import Blueprint, request, jsonify
from database.db import fetch_one
from decorator.token import token_required

user_bp = Blueprint("user", __name__)
@user_bp.route('/getUserinfo', methods=['GET'])
@token_required
def get_userinfo():
    result = fetch_all("select * from tb_subject") # 调用查询方法
    return jsonify({"code": 200, "data": result})
```

### 方式二：

使用 SQLAlchemy  自带连接池和 orm 架构

安装

```shell
pip install sqlalchemy 
```

创建 database / db.py

```python
import os
from functools import lru_cache
from typing import Any, Dict, List, Optional, Tuple
from functools import lru_cache
from sqlalchemy import create_engine, MetaData, Table, select, insert, update, delete, text
from sqlalchemy.engine import Engine, Result,URL
from contextlib import contextmanager

# 从环境变量读取（生产更安全）
DB_USER = os.getenv("DB_USER", "root")
DB_PASS = os.getenv("DB_PASS", "")
DB_HOST = os.getenv("DB_HOST", "127.0.0.1")
DB_PORT = int(os.getenv("DB_PORT", "3307"))
DB_NAME = os.getenv("DB_NAME", "vote")

def sqlalchemy_url():
    return URL.create(
        drivername="mysql+pymysql",
        username=DB_USER,
        password=DB_PASS,
        host=DB_HOST,
        port=DB_PORT,
        database=DB_NAME,
        query={"charset": "utf8mb4"},  # 防止乱码
    )

# --- 1) Engine 全局单例 ---
@lru_cache(maxsize=1)
def get_engine() -> Engine:
    return create_engine(
        sqlalchemy_url(),
        pool_pre_ping=True,     # 探活，避免 “MySQL server has gone away”
        pool_recycle=3600,      # 回收时间（秒）
        future=True,            # 使用 2.0 风格 API
        echo=False,             # 调试时可设 True
    )

# --- 2) 元数据与表缓存（反射已存在表结构） ---
_metadata = MetaData()
@lru_cache(maxsize=128)
def get_table(table_name: str) -> Table:
    eng = get_engine()
    return Table(table_name, _metadata, autoload_with=eng)

# --- 3) 上下文：读用 connect，写用 begin（带事务） ---
@contextmanager
def connect():
    conn = get_engine().connect()
    try:
        yield conn
    finally:
        conn.close()

@contextmanager
def begin():
    with get_engine().begin() as conn:  # 自动 commit/rollback
        yield conn

# --- 4) 通用 CRUD（无模型类，按表名操作） ---
def create_row(table: str, values: Dict[str, Any]) -> int:
    t = get_table(table)
    with begin() as conn:
        res: Result = conn.execute(insert(t).values(**values))
        # MySQL 下可用 lastrowid；跨库可返回受影响行数或主键查询
        return res.lastrowid or 0

def get_by_id(table: str, pk_name: str, pk_value: Any) -> Optional[Dict[str, Any]]:
    t = get_table(table)
    stmt = select(t).where(getattr(t.c, pk_name) == pk_value)
    with connect() as conn:
        row = conn.execute(stmt).mappings().first()
        return dict(row) if row else None

def query_rows(
    table: str,
    where: Optional[List[Tuple[str, str, Any]]] = None,  # [(col, op, val)], 如 [("name","=", "Tom")]
    order_by: Optional[str] = None,
    limit: Optional[int] = None,
    offset: Optional[int] = None,
) -> List[Dict[str, Any]]:
    t = get_table(table)
    stmt = select(t)
    # 动态 where
    if where:
        from sqlalchemy import and_, or_
        conds = []
        for col, op, val in where:
            col_expr = getattr(t.c, col)
            if op == "=":
                conds.append(col_expr == val)
            elif op == "!=":
                conds.append(col_expr != val)
            elif op == ">":
                conds.append(col_expr > val)
            elif op == "<":
                conds.append(col_expr < val)
            elif op.lower() == "like":
                conds.append(col_expr.like(val))
            elif op.lower() == "in" and isinstance(val, (list, tuple, set)):
                conds.append(col_expr.in_(list(val)))
            else:
                raise ValueError(f"Unsupported operator: {op}")
        from sqlalchemy import and_
        stmt = stmt.where(and_(*conds))

    # 排序
    if order_by:
        # 支持 "id desc" / "name asc"
        parts = order_by.split()
        col = getattr(t.c, parts[0])
        if len(parts) > 1 and parts[1].lower() == "desc":
            col = col.desc()
        stmt = stmt.order_by(col)

    # 分页
    if limit is not None:
        stmt = stmt.limit(limit)
    if offset is not None:
        stmt = stmt.offset(offset)

    with connect() as conn:
        rows = conn.execute(stmt).mappings().all()
        return [dict(r) for r in rows]

def update_by_id(table: str, pk_name: str, pk_value: Any, values: Dict[str, Any]) -> int:
    t = get_table(table)
    with begin() as conn:
        res: Result = conn.execute(
            update(t).where(getattr(t.c, pk_name) == pk_value).values(**values)
        )
        return res.rowcount

def delete_by_id(table: str, pk_name: str, pk_value: Any) -> int:
    t = get_table(table)
    with begin() as conn:
        res: Result = conn.execute(
            delete(t).where(getattr(t.c, pk_name) == pk_value)
        )
        return res.rowcount

# --- 5) 如需直接写 SQL（可选） ---
def exec_sql(sql: str, params: Optional[Dict[str, Any]] = None) -> List[Dict[str, Any]]:
    with connect() as conn:
        rows = conn.execute(text(sql), params or {}).mappings().all()
        return [dict(r) for r in rows]
```

使用

```python
from flask import Blueprint, request, jsonify

from database.db2 import get_by_id

admin_bp = Blueprint("admin", __name__)

@admin_bp.route("/userList", methods=['POST'])
def user_list():
    row = get_by_id("tb_subject", "name", "node第一")
    return jsonify({"code":200,"data":row,"message":"success"})

```

---

## token装饰器

创建装饰器 decorator / token.py 

```python
from functools import wraps
from flask import request, jsonify


def token_required(f):
    @wraps(f)
    def decorated_function(*args, **kwargs):
        token = request.headers.get('Authorization')  # 可以从 headers 中获取
        if not token:
            return jsonify({"code": 401, "message": "Missing token"}), 401
        # 你也可以加 token 的合法性验证逻辑
        return f(*args, **kwargs)
    return decorated_function
```

在接口中使用

```python
from flask import Blueprint, request, jsonify
# 引入 token 装饰器
from decorator.token import token_required

user_bp = Blueprint("user", __name__)
@user_bp.route('/getUserinfo', methods=['GET'])
@token_required
def get_userinfo():
    return jsonify({"code": 200, "data": []})
```

---

## flask 解决跨域问题

安装

```shell
pip install flask-cors
```

配置文件 mian.py

```python
from flask import Flask

app = Flask(__name__)
CORS(app)
...
if __name__ == '__main__':
    app.run(host='127.0.0.1', port=3000)
```

---

## 项目部署：

因为 flask 和 node express类似，没有工程化构建过程，因此不需要打包

### unbtu系统

安装 conda

```shell
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

运行

```shell
source ~/.bashrc  # 或 zshrc
```

安装 python 指定版本

```shell
conda create -n flask312 python=3.12
conda activate flask312
```

安装 gunicorn 作为后台服务管理工具（等于node中的pm2）

```shell
pip install gunicorn
```

启动项目

```python
gunicorn -w 4 -b 0.0.0.0:3000 main:app
# -w 4 开启4个线程
# -b 0.0.0.0:3000 供外网访问
# main:app 项目的入口文件:在服务中的备注名称
```

查看运行 

```shell
ps aux | grep gunicorn
# 输出示例
# user     12345  0.2  ... gunicorn: master [demo01:app]
```

关闭服务

```shell
kill -9 12345  # 替换为 master 进程的 PID
```



