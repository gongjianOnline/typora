# mongoose使用

## hello world

初始化项目并安装依赖

```shell
npm init
npm install mongoose
npm install -S saslprep
```

新建app.js文件

```javascript
const mongoose = require('mongoose');
mongoose.connect(
    // 地址解读 mongodb://数据库的用户名:密码@ip:端口号/数据库名
    'mongodb://test:123456@192.168.33.128:27017/testdb',
	{
  		useNewUrlParser:true, // 使用新的URL解析器,用于解决一些安全性问题
  		useUnifiedTopology:true //统一拓扑结构 
	}
);

const User = mongoose.model("users",{name:String,age:Number,email:String})
const kitty = new User({
  name:"张无忌",
  age:25,
  email:'15031617367@163.com'
})
kitty.save().then(()=>console.log('meow'))
```

