---
title: React+Nodejs项目搭建记录
date: 2018-12-25 13:18:55
tags: 
- "Nodejs"
- "React"
categories: 
- "Nodejs"
- "React"
---
# 前言

平时的工作中要么是使用dva这种高度封装、开箱即用的框架，要么是基于别人搭建好的项目进行开发，十分缺乏从无到有搭建项目的经验，也缺乏对React框架的深刻理解。因此这次的毕业设计决定作为一次学习，从无到有的搭建一个前端基于React+Redux，后端基于nodejs的项目架子。

# 项目搭建

## 一、Nodejs部分

本次项目不再使用Java作为后端语言，而是使用nodejs开发后端部分，数据库使用MySQL。

### 服务器雏形生成（脚手架）

nodejs应用程序常用的一个框架是express，也是本次开发选用的框架，具体使用可以参考[express官方说明文档](https://expressjs.com/zh-cn/)。

运行命令`$ express -e myapp && cd myapp && npm install`，使用express脚手架工具生成服务端雏形。

### 服务端项目架构修改

既然是后端代码，就仿照之前学习Java的经验，进行简单的MVC分层。View层即视图层，在服务端雏形中已经存在，这部分等到后端部分搭建完后再着手修改。

首先实现Model层，即数据模型层。

在项目根目录下创建一个`database`文件夹，这个文件夹就用来存放所有属于Model层的文件，现规定该文件夹下的文件**统一使用`.db.js`作为后缀**。这一层主要负责与数据库交互。

node要连接mysql需要用到第三方的mysql库，先进行安装，命令如下：

    $ npm install mysql -save

新建一个`config.db.js`文件，项目中所有关于数据库连接的代码都放在这里。

这里不再遵循之前的做法，每次发送请求都重新创建数据库连接，创建数据库连接是一个十分消耗性能的操作。这里使用数据库连接池的方式获取数据库连接。对于数据库连接池的概念模糊的地方可以查看[数据库连接池原理](https://blog.csdn.net/shuaihj/article/details/14223015)进行了解。

在nodejs项目中创建mysql数据库的连接池的操作具体可以查看[express+mysql连接池的创建方式](https://blog.csdn.net/qq_35374517/article/details/77991262)。

最终`config.db.js`文件中内容如下：

```js
/**
 * @description 数据库配置文件
 * @author NaiyingZhang <fnine59@163.com>
 * @date 2018-12-24
 */
var mysql = require('mysql'); // 引入所需的第三方mysql库

var pool = mysql.createPool({ // 创建连接池
  host: 'localhost',
  port: 3306, // 端口号
  user: 'root', // 用户名
  password: '*******', // 密码
  database: 'xxxxxx', // 数据库名
});

var db = {}; // 创建一个空对象用于export出去

/**
 * 从数据库连接池中获取数据库连接的方法
 */
db.conn = function (callback){
  pool.getConnection(function (err, connection) { // 从连接池中获取到数据库连接
    console.log('connect start...');
    if(err) { // 如果出现异常直接抛出
      throw err;
    } else {
      if(typeof callback === 'function'){ // 如果传入了callback函数，执行它并将得到的连接传入
        callback(connection);
      }
    }
    connection.release(); // 释放连接
    console.log('connect end...');
  })
};

module.exports = db;
```

至此，`config.db.js`文件编写完毕，上面的代码中提供了一个公用的获取数据库连接的方法。得到了数据库连接后，下一步是执行sql语句。在`routes`文件夹下创建一个放置后端开发用工具方法的类`helper.js`。

```js
/**
 * @description 后端用工具模块
 * @author NaiyingZhang <fnine59@163.com>
 * @date 2018-12-24
 */

var db = require('../database/config.db'); // 引入数据库配置文件暴露出的对象

var helper = {
  /**
   * 执行sql语句的方法，该方法接收一个opt对象，该对象属性如下：
   * name：请求名
   * sql：要执行的sql语句
   * callback：该请求的回调函数
   */
  db_query: function(opt) {
    // 调用db对象中的conn方法并传入一个callback函数，参数即为拿到的数据库连接
    db.conn(function (connection){
      console.log(`request method: ${opt.name}`)
      connection.query(opt.sql, function(err, res) {
    		if (err) {
    			console.log(`request method ${opt.name} err: + ${err}`);
    		} else {
    			console.log(`request method ${opt.name} success!`);
    			if (typeof(opt.callback) === 'function') {
    				opt.callback(err, res);
    			}
    		}
    	})
    });
  },
};

module.exports = helper;
```

上面的代码中，建立了一个用于执行sql语句的方法，并且还会执行请求成功后的回调。接下来看下刚才封装好的方法的具体使用。先在数据库中创建一张测试用表命名为`test`，在`database`文件夹下创建`goods.db.js`文件，内容如下。

```js
/**
 * @description 物品表数据模型层
 * @author NaiyingZhang <fnine59@163.com>
 * @date 2018-12-24
 */

var helper = require('../routes/helper');

/*物品模块 构造方法*/
var Goods = function(goods) {
    this.props = goods.props;
};

Goods.prototype.getTestAllItems = function(callback) { // model层方法，用于与数据库交互
    var _sql = "select * from test";
    helper.db_query({
        sql: _sql,
        name: 'getTestAllItems',
        callback: callback
    })
}

module.exports = Goods;
```

至此，Model层的工作进行完毕，然后来编写Control层，即控制层。这层主要负责定义和调用Model，也可以可以决定要显示哪一个View。因为这部分与后端路由有关，因此放在脚手架为我们搭建好的`routes/services`文件夹下。

在该文件夹下创建`goods.ctrl.js`文件，并约定**Control层的文件统一使用`.ctrl.js`作为后缀**。

```js
/**
 * @description 物品表控制层
 * @author NaiyingZhang <fnine59@163.com>
 * @date 2018-12-24
 */

var Goods = require('../../database/goods.db'); // 从Model层拿到Goods的构造方法
var Helper = require('../helper');

module.exports = {
    // 模块初始化
    init: function(app) {
        app.get('/user', this.doGetTestAllItems) // 使用get方法请求/user时，执行doGetTestAllItems
    },

    // 获取测试表中的所有信息
    doGetTestAllItems: function(req, res) { // Control层中用于调用Model层的方法
        var props = {}; // 不传入参数
        var goods = new Goods({ props: props }); // 创建goods实例
        goods.getTestAllItems(function(err, data) { // 调用Model层方法，并传入callback函数
            if (data.length) {
                return res.send({
                    code: 200,
                    data: data
                })
            } else {
                console.log(err)
                return res.send({
                    code: 500,
                    message: '出错了'
                })
            }
        })
    }
}
```

编写完Control层，再来做些其他配置。

`routes/api.js`文件内容如下：

```js
var express = require('express');
var router = express.Router();
var fs = require('fs');

var FS_PATH_SERVICES = './routes/services/';
var REQUIRE_PATH_SERVICES = './services/';

router.options('*', function (req, res, next) {
    next();
});

try {
    var list = fs.readdirSync(FS_PATH_SERVICES);
    for (var e; list.length && (e = list.shift());) {
    	var service = require(REQUIRE_PATH_SERVICES + e);
    	service.init && service.init(router);
    }
} catch(e) {
    console.log(e);
}

module.exports = router;
```

因为前端也会涉及到路由，为了做区分，我们为后端提供的API做一层单独的区分，修改app.js文件如下：

```js
var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');
var logger = require('morgan');

var routes = require('./routes/index');
var api = require('./routes/api'); // 添加这句

var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', routes);
app.use('/api', api); // 添加这句

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;
```

看下引用的`./routes/index`中有什么内容：

```js
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;
```

至此，后端架子基本搭建完毕，重启node服务，可以试着请求一下[http://localhost/api/user](http://localhost/api/user)来测试刚才写的案例接口是否正常。

请求成功后截图如下：

![请求成功](https://ws1.sinaimg.cn/large/006gU7ahly1fyitxxk98gj30bf07974f.jpg)