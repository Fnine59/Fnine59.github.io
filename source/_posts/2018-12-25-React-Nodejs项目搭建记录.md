---
title: React+Nodejs项目搭建记录
date: 2018-12-25 13:18:55
tags: 
- "Nodejs"
- "React"
categories: "技术"
---
# 前言

平时的工作中要么是使用dva这种高度封装、开箱即用的框架，要么是基于别人搭建好的项目进行开发，十分缺乏从无到有搭建项目的经验，也缺乏对React框架的深刻理解。因此这次的毕业设计决定作为一次学习，从无到有的搭建一个前端基于React+Redux，后端基于nodejs的项目。

# 项目搭建

之前参照的案例是先搭建后端部分，然后依据后端脚手架搭建出的项目结构再去搭建前端项目，但最终搭建出的项目前后端并没有完全分离。因此推翻重来，这次搭建从前端部分开始。

## 项目初始化

首先创建文件夹supply-chain，并进入该文件夹。

```
mkdir supply-chain && cd blog
```

接着使用npm初始化这个项目。

```
npm init
```

基础配置可以仔细填写一下，也直接enter全部跳过，手误填错了也不要紧，因为这些配置之后都可以在package.json文件中进行配置。

之后再创建两个文件夹，分别用于放前端和后端的代码。

```
mkdir client && mkdir server
```

## client前端部分

### 前端部分项目结构搭建

因为我们会使用es6和react的语法，需要用到babel进行编译，因此这里首先准备两个文件夹src和dist，分别用于存放源代码和编译后的代码。

```
mkdir src && mkdir dist
```

我们会将js和css单独打包出来，所以在dist文件夹下创建js、css和img文件夹分别用来存放编译后的js、css和图片文件，同时在dist文件夹下创建index.html入口文件。

index.html文件中的内容如下。
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>餐饮供应链物资管理系统</title>
</head>
<body>
  <div id="app"></div>
  <!-- 记得引入打包后的js文件 -->
  <script src="./js/vendor.bundle.js"></script> 
  <script src="./js/index.bundle.js"></script>
</body>
</html>
```

之后来创建src文件夹下的部分，这里就不一一列举了，直接来看搭建完毕后的项目结构。

```js
+ supply-chain
   + client
      + dist // 编译并打包后的文件
        + img
        + js
        + css
        + index.html // 前端入口文件
      + src
         + assets // 图片等静态资源
         + components // 一些公用组件
         + containers // 容器组件
         + redux // 和redux有关的文件
            + actions // action文件
            + reducers // reducer文件
            + store // 初始化的store
         + routes // 路由相关文件
            + routes.js
         + index.js // 整个前端项目的入口文件
   + server // 服务端文件
   + package.json   
```

安装项目所需的依赖。

```
cnpm install react react-dom redux react-redux react-router css-loader style-loader node-less less-loader file-loader url-loader autoprefixer --save-dev

cnpm install antd --save
```

### webpack的使用与配置

首先安装webpack所需的依赖。

```
cnpm install webpack webpack-cli webpack-dev-server --save-dev
```

然后在项目根目录下创建一个webpack.config.js文件，文件配置如下。

```js
// 引入必须的包
var webpack = require('webpack'),
    path = require('path'),
    UglifyJsPlugin = require('uglifyjs-webpack-plugin');

var config = {
    entry: { // 打包入口，即webpack从哪个文件开始查找形成依赖网并打包
        index: path.resolve(__dirname, "client/src/index.js"),
        vendor: [  // 将react和react-dom这些单独打包出来，减小打包文件体积
            "react",
            "react-dom",
        ]
    },
    output: { // 打包目标路径，即打包后的文件输出到哪个路径
        path: path.resolve(__dirname, "client/dist"),
        filename: "js/[name].bundle.js" // 这个配置最终会生成index.bundle.js和vendor.bundle.js两个文件
    },
    optimization: {
      // 公共代码抽取插件，提取被重复引入的文件，单独生成一个或多个文件，这样避免在多入口重复打包文件，本插件无需安装，是内置的
      splitChunks: { 
        cacheGroups: {
          vendor: {
            chunks: "initial",
            test: "vendor",
            name: "vendor",
            enforce: true
          }
        }
      },
      minimizer: [ // 压缩打包后的代码
        new UglifyJsPlugin({
          cache: true,
          parallel: true,
          uglifyOptions: {
            compress: false,
            ecma: 6,
            mangle: true
          },
          sourceMap: true
        })
      ]
    },
    resolve: {
        //自动扩展文件后缀名，意味着我们require模块可以省略不写后缀名
        extensions: ['.js','.jsx'],
        //模块别名定义，方便后续直接引用别名，无须写很长的引入路径
        alias: {
          'components': path.resolve(__dirname,'src/components')
        }
    },
    module: {
      // webpack4之后这里的配置不再叫做loader而叫rules，一定要注意，否则会报错
      rules: [{ 
          test: /\.js|.jsx$/,
          exclude: /node_modules/,
          loader: "babel-loader", // 这里是babel的配置，babel的安装与使用在下面一节
      }, {
          test: /\.(less|css)$/,  // 打包less和css文件
          use: [
            "style-loader",
            "css-loader",
            "less-loader",
          ]
      }, {
          test: /\.(png|jpg|jpng|eot|ttf)$/, // 打包图片和字体文件
          loader: 'url-loader?limit=8192&name=images/[name].[ext]'
      }]
    },
    plugins: [
      new webpack.DefinePlugin({
          "process.env": { 
              NODE_ENV: JSON.stringify("production") 
          }
      }),
    ],
    devServer: {
      contentBase: path.join(__dirname, "client/dist"),
      port: 8000
    },
}

module.exports = config;
```

关于上面的配置有几个需要注意的点如下：

1. 使用了webpack4之后，上面的很多配置都可以写为命令行的形式，也就是package.json文件中配置webpack相关的命令时，可以多带几个参数，写在命令行中的参数方式和写在配置文件中的方式效果是相同的

2. 关于公共代码的抽取，之前我们经常使用的是CommonsChunkPlugin，但在webpack4中这个插件已经弃用，我们要使用optimization.splitChunks代替，splitChunks是内置的，无需再次安装。该插件的作用是提取被重复引入的文件，单独生成一个或多个文件，这样避免在多入口重复打包文件。具体可以查看[webpack4入门5——插件](https://www.jianshu.com/p/3066d96aec8b)

3. 注意合理配置resolve.extensions，减少文件的查找，以提高性能。这一配置项的默认值是：`extensions:['.js','.json']`，当导入语句没带文件后缀时，Webpack会根据extensions定义的后缀列表进行文件查找，所以列表值应该尽量少，频率高的文件类型的后缀应该写在前面，源码中的导入语句应该尽可能的写上文件后缀，如`require(./data)`要写成`require(./data.json)`。具体可以参考[三十分钟掌握Webpack性能优化](https://juejin.im/post/5b652b036fb9a04fa01d616b)

4. webpack4之后不再使用module.loaders这一配置项，改用module.rules，如果依旧使用loaders会报错。

5. 关于DefinePlugin的使用可以参考[webpack选择性编译DefinePlugin（打包自动剔除测试数据）](https://segmentfault.com/a/1190000011530718)

6. webpack-dev-server配置的contentBase会影响到index.html中引入js文件的路径，具体可以参考[【webpack】webpack-dev-server生猛上手——让我们来搭一个webpack的微服务器吧！](http://www.cnblogs.com/penghuwan/p/6941616.html)

写好webpack配置文件之后，最后一步就是在package.json文件中添加我们需要的命令，让webpack真正用起来，具体配置如下。

```js
  "scripts": {
    "start": "webpack-dev-server --mode development --hot --inline", // 用于启动webpack-dev-server，开启热更新
    "build:dev": "webpack --mode development", // 开发环境打包
    "build:pro": "webpack --mode production" // 生产环境打包
  },
```

上面的配置中也有一点需要注意，webpack4引入了production（生产）和development（开发）模式，因此使用webpack4时，启动webpack-dev-server时一定要**指定mode为开发模式**，因为这涉及到热更新的速度，本质上是影响到了打包的速度。

这是因为webpack-dev-server实际上也是进行了一次打包，只不过输出的位置不在我们指定的dist目录中，而是在内存中，因此在dist目录中是看不到输出后的文件的。但正因为这一过程实际上也是一次打包，因此指定是production模式还是development模式就显得十分重要，因为**production mode（生产模式）可以开箱即用地进行各种优化，包括压缩，作用域提升，tree-shaking等；而development mode（开发模式）针对速度进行了优化，仅仅提供了一种不压缩的 bundle**。具体可以参考[Webpack 4 教程：从零配置到生产发布（2018）](https://www.css88.com/archives/9436)。

### babel的使用与配置

Babel是一个JavaScript编译器，可以将es6、jsx等编译为浏览器可以识别的语法。

首先安装babel相关的依赖。

```
cnpm install babel-cli babel-core babel-loader babel-preset-es2015 babel-preset-react babel-preset-stage-0 babel-plugin-import babel-cli --save-dev
```

这里有一点需要注意的是，如果babel-core的版本是6.x，而babel-loader的版本是8.x，则要将babel-loader的版本降至7.x，否则webpack编译时会报错找不到babel-core模块，具体可以参考[
webpack.config.js配置遇到Error: Cannot find module '@babel/core'](https://blog.csdn.net/ma_jiang/article/details/82872000)。

安装完依赖后，首先在webpack中配置babel，给config.module.rules增加一条配置即可。

```js
module: {
  // webpack4之后这里的配置不再叫做loader而叫rules，一定要注意，否则会报错
  rules: [{ 
      test: /\.js|.jsx$/,
      exclude: /node_modules/,
      loader: "babel-loader", // 这里是新增的配置
  }, {
      test: /\.(less|css)$/,  // 打包less和css文件
      use: [
        "style-loader",
        "css-loader",
        "less-loader",
      ]
  }, {
      test: /\.(png|jpg|jpng|eot|ttf)$/, // 打包图片和字体文件
      loader: 'url-loader?limit=8192&name=images/[name].[ext]'
  }]
},
```

上面的配置中，test一般是一段正则，用来匹配对应类型的文件；exclude是应该被忽略的文件，这里指定了node_modules。

除了上面的配置外，还也要在项目根目录下新建一个.babelrc文件，内容如下：

```js
{
  "presets": ["es2015", "react", "stage-0"],
  "plugins": [ // 这个是配置的环境，不用ant design可以直接去掉plugins属性
      [
          "import",
          {
              "libraryName": "antd",
              "style": "css"
          }
      ]
  ]
}
```

至此，webpack及babel就配置完成了，我们在index.js文件中写一段简单的测试代码如下：

```
import React from 'react';
import { render } from 'react-dom';

render(
  <h1>hello world</h1>,
    document.getElementById('app'),
);
```

此时在命令行中运行`npm start`或`npm build:dev`或`npm build:pro`，可以查看项目运行和打包的效果，注意如果要查看build后的效果，需要手动打开dist文件夹中的index.html文件查看。

### eslint使用及配置

项目的基本结构其实已经搭建好了，接下来配置一下eslint来保证代码的编写格式正确。

首先安装所需的依赖。

```
cnpm install eslint eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react babel-eslint --save-dev
```

安装完依赖后，在项目根目录下创建两个文件。

.eslintrc：

```
{
  "extends": "eslint-config-airbnb",
  "globals": {
    "APP": true,
    "window": true,
    "document": true
  },
  "plugins": [
    "react",
    "import"
  ],
  "parser": "babel-eslint",
  "parserOptions": {
    "sourceType": "module"
  },
  "settings": {
    "import/resolver": {
      "node": {
        "extensions": [".js", ".jsx"]
      }
    }
  },
  "rules": {
    "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
    "no-console": 0,
    "react/no-multi-comp": 0,
    "react/prop-types": 1,
    "react/forbid-prop-types": 0,
    "import/no-unresolved": [2, { "commonjs": true }],
    "import/no-duplicates": 2,
    "complexity": [2, 15],
    "max-depth": [2, 3],
    "max-statements": [2, 50],
    "max-nested-callbacks": [2, 3],
    "camelcase": 2,
    "linebreak-style": 0,
    "import/no-dynamic-require": 0,
    "global-require": 0,
    "func-names": 0
  }
}
```

.eslintignore：

```
/node_module/*
/bower_componenets/*
webpack.config.js
/public/**
```

### editorConfig

editorConfig用于定义和维护一致的编码风格，是为了不同的开发人员无需设置自己的编译器，只要有这样一个配置文件即可保持统一风格。具体可以参考[editorConfig编辑器配置](https://www.jianshu.com/p/00ac7bd5e74e)

文件内容如下：

```
# http://editorconfig.org
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.md]
trim_trailing_whitespace = false

[Makefile]
indent_style = tab
```

### .gitignore

为了避免不必要的文件被上传到git仓库，在项目根目录下创建一个.gitignore文件，用来指定哪些文件在上传到git仓库时被忽略。

文件内容如下：

```
node_modules
client/dist/
```

到此，项目基本框架其实搭建的差不多了，像是eslint，editorConfig，gitignore，都不是仅仅局限于前端的配置，而是作用于整个项目。

## server后端部分

本次项目不再使用Java作为后端语言，而是使用nodejs开发后端部分，数据库使用MySQL。

### 服务器雏形生成（脚手架）

nodejs应用程序常用的一个框架是express，也是本次开发选用的框架，具体使用可以参考[express官方说明文档](https://expressjs.com/zh-cn/)。

首先安装express，和express应用生成器express-generator（这个不需要保存项目依赖中，全局安装可以使用即可）。

```
cnpm install express --save
cnpm install express-generator -g
```

把之前的server文件夹删除，然后在项目根目录下使用express自动生成server文件夹。

```
rm -rf server && server
```

删除自动生成的项目结构中不需要的部分，即views文件夹和public文件夹。

```
rm -rf views && public
```

这时候会发现server目录下面也有一个package.json文件，这个文件保存着express需要的依赖，我们把里面的内容合并到外面的package.json里面然后再删除，之后记得用npm install来重新安装一遍项目依赖。

### 后端配置

这里需要用到一个bodyParser，首先安装它。

```
cnpm i -S bodyParser // -S就是保存到dependences
```

打开app.js文件，发现里面已经已经写好了代码，但是这些代码并不是完全符合需求，比如我们不想使用jade或ejs模板引擎，而是想用前后端分离的开发方式，因此需要修改一下代码。修改后的代码如下：

```
import express from 'express'
import bodyParser from 'body-parser'
import logger from 'morgan'
import cookieParser from 'cookie-parser'

import api from './utils/api'

let app = express();
const port = 3000;

app.use(logger('dev')); //命令行里面显示请求
app.use(bodyParser.json()); //解析json
app.use(bodyParser.urlencoded({ extended: true }));
app.use(cookieParser()); //解析cookie

app.use('/api', api);

app.get("/", (req, res) => {
    res.render("index")
})

app.listen(port, () => {
    console.log(`server is running on port ${port}`);
});
```

可以看到上面的配置中用到了一个名为api的文件，这个文件的作用，其实是将`app.requestMethod`部分的代码都放到各自的Control层文件中，具体实现如下：

```js
import express from 'express';
import fs from 'fs';

const router = express.Router();
const ABSOLUTE_PATH_SERVICES = './server/services';
const REQUIRE_PATH_SERVICES = '../services/';

router.options('*', (req, res, next) => {
  next();
});

try {
  const list = fs.readdirSync(ABSOLUTE_PATH_SERVICES);
  list.forEach((item) => {
    const service = require(REQUIRE_PATH_SERVICES + item);
    if (service.init) {
      // 将express.Router()传入每个service的init，方便在每个service的init中调用相应的方法
      service.init(router);
    }
  });
} catch (e) {
  console.log('api配置错误', e);
}

module.exports = router;
```

有了上面的代码之后，类似`app.get("/", (req, res) => { // ... })`这种代码就不用再现在app.js文件中了，只需要在services文件夹中每个对应文件里，配置相应的init属性，在init属性的值中添加相应的配置即可。

配置完这些，只需要在命令行里面输入node app.js就可以运行后端项目了。

### 在后端项目中使用es6

之前提取出来的.babelrc文件中已经配置好了转码规则，而babel-cli自带了一个babel-node命令，它让在node环境里面运行es6的语法成为可能。

因此我们可以将后端部分的代码修改为es6的语法。

### 后端项目hot reload

使用nodemon来达到后端项目hot reload（热加载）的效果。

首先安装nodemon。

```
cnpm install nodemon -g
```

它的用法和node一样，我们可以直接nodemon app.js来开启node服务，当你修改node代码时，它会自动重启node。

修改package.json文件，在scripts属性中添加命令使用nodemon。

```js
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "server": "nodemon ./server/app.js --exec babel-node",
    "start": "webpack-dev-server --mode development --hot --inline",
    "build:dev": "webpack --mode development",
    "build:pro": "webpack --mode production"
},
```

此时就可以使用`npm run server`命令启动后端服务了，并且，如果后端代码有任何修改，后端服务会自动监听并重启，无需手动刷新。

### 后端mvc结构搭建

在server文件夹下创建几个目录。

- utils 用于存放一些工具类
- models 用于存放Model层（数据模型层）的文件，这一层主要负责与数据库交互。
- services 这个文件夹存放的主要是Control层的文件，负责调用Model层中的方法。

### 连接mysql数据库

基于刚才搭建好的项目框架，开始编写连接数据库的代码。首先在utils文件夹下创建一个connection.js文件，这个文件中编写了获取数据库连接的代码。

需要注意的一点是，这里不再遵循之前的做法，每次发送请求都重新创建数据库连接，创建数据库连接是一个十分消耗性能的操作。这里使用数据库连接池的方式获取数据库连接。对于数据库连接池的概念模糊的地方可以参考[数据库连接池原理](https://blog.csdn.net/shuaihj/article/details/14223015)。而在nodejs项目中创建mysql数据库的连接池的操作具体可以参考[express+mysql连接池的创建方式](https://blog.csdn.net/qq_35374517/article/details/77991262)。


具体内容如下：

```js
/**
 * @description 数据库配置文件
 * @author NaiyingZhang <fnine59@163.com>
 * @date 2018-12-24
 */

import mysql from 'mysql'; // 引入所需的mysql库

const pool = mysql.createPool({ // 创建连接池
  host: 'localhost',
  port: 3306, // 端口号
  user: 'root', // 用户名
  password: 'xxxxxx', // 数据库密码
  database: 'supply_chain', // 数据库名称
});

const db = {};

/**
 * 从数据库连接池中获取数据库连接的方法
 */
db.conn = function (callback) {
  pool.getConnection((err, connection) => { // 从连接池中获取到数据库连接
    console.log('connect start...');
    if (err) { // 如果出现异常直接抛出
      throw err;
    } else if (typeof callback === 'function') { // 如果传入了callback函数，执行它并将得到的连接传入
      callback(connection);
    }
    connection.release(); // 释放连接
    console.log('connect end...');
  });
};

module.exports = db;
```

可以看到，上面的代码实际上是提供了一个公用的获取数据库连接的方法。得到了数据库连接后，下一步是执行sql语句。在utils文件夹下创建一个放置后端开发用工具方法的文件helper.js。

具体内容如下：

```js
/**
 * @description 后端用工具模块
 * @author NaiyingZhang <fnine59@163.com>
 * @date 2018-12-24
 */

import crypto from 'crypto';
import db from './connection';

const helper = {
  // 执行sql语句
  doSql(opt) {
    db.conn((connection) => {
      console.log(`request method: ${opt.name}`);
      connection.query(opt.sql, (err, res) => {
        if (err) {
          console.log(`request method ${opt.name} err: + ${err}`);
        } else {
          console.log(`request method ${opt.name} success!`);
          if (typeof opt.callback === 'function') {
            opt.callback(err, res);
          }
        }
      });
    });
  }
}

module.exports = helper;
```

上面的代码中，建立了一个用于执行sql语句的方法，并且还会执行成功后的回调。接下来看下刚才封装好的方法的具体使用。先在数据库中创建一张测试用表命名为`test`，在models文件夹下创建goods.js文件，内容如下：

```js
/**
 * @description 物品表数据模型层
 * @author NaiyingZhang <fnine59@163.com>
 * @date 2018-12-24
 */

import helper from '../utils/helper';

/* 物品模块 构造方法*/
const Goods = function (goods) {
  this.props = goods.props;
};

/* 获取全部数据,测试接口使用*/
Goods.prototype.getTestAllItems = function (callback) {
  const sql = 'select * from test';
  helper.doSql({
    sql,
    name: 'getTestAllItems',
    callback,
  });
};

module.exports = Goods;
```

在services文件夹下创建文件goods.js，编写文件内容如下：

```js
/**
 * @description 物品表控制层
 * @author NaiyingZhang <fnine59@163.com>
 * @date 2018-12-24
 */

import Goods from '../models/goods';

module.exports = {
  init(app) {
    app.get('/user', this.doGetTestAllItems);
  },
    // 获取所有用户信息
  doGetTestAllItems(req, res) {
    const props = {};
    const goods = new Goods({ props });
    goods.getTestAllItems((err, data) => {
      if (data.length) {
        return res.send({
          code: 200,
          data,
        });
      }
      console.log(err);
      return res.send({
        code: 500,
        message: '出错了',
      });
    });
  },
};
```

可以看到上面的代码中添加了一个`init`属性，这个属性之所以起作用就是因为之前配置过的api.js文件。

配置到这里，就可以发送请求去请求数据库中的数据了。

打开postman，在地址栏输入`localhost:3000/api/user`，使用get方法，查看返回结果。

![请求结果](https://ws1.sinaimg.cn/large/006gU7ahly1fymm1i19j2j30a80b4wep.jpg)

至此，整个项目结构基本搭建完毕。

## 项目运行

- 使用`git clone 项目地址`命令拷贝项目到本地
- 运行`npm run server`命令启动后端服务
- 运行`npm start`命令启动前端服务
- 浏览器访问[localhost:8000](localhost:8000)即可查看效果

- 开发环境前端打包命令`npm run build:dev`
- 生产环境前端打包命令`npm run build:pro`
- 接口请求地址`http://localhost:3000/api/xxx`，localhost后期可以替换为ip