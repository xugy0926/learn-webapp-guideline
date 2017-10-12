# 认识路由

在浏览器中输入`localhost:3000/`和`localhost:3000/users`，分别会看到不同的页面。first-app对两个不同的地址请求做出了不同的反应。

当first-app收到浏览器的请求后，会对`/`和`/users`做出不同的反应，换句话说是会针对不同的路由执行不同的代码。判断是`/`后就执行routes/index.js，判断是`/users`后就执行routes/users.js。

假如输入localhost:3000/posts，first-app并没有处理`/posts`，会返回错误页面的处理。

![](/assets/route-map.png)

## 加载路由的逻辑处理模块

在first-app/app.js文件中有如下代码。

```
var index = require('./routes/index');
var users = require('./routes/users');
```

通过require\(\)分别加载了./routes/index和./routes/users两个文件模块。这两个文件模块是处理路由的逻辑文件。

## 设置路由和逻辑处理模块

在first-app/app.js文件中设置了路由和逻辑处理莫管的关系。

```
var index = require('./routes/index');
var users = require('./routes/users');

...

app.use('/', index);
app.use('/users', users);

...
```

当用户输入地址localhost:3000/（路由为/） 就执行 index模块，如果输入地址 localhost:3000/users （路由为/users）就执行 users模块。除了`/`和`/users`之外就没有处理其他路由的模块。

在first-app/app.js中针对其他路由都会执行统一的代码并返回一个错误页面。

```
// catch 404 and forward to error handler

app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});
```

输入 localhost:3000/posts，因为`/posts`并没有被处理，所以返回错误页面。

## 路由和逻辑处理

路由`/`对应的逻辑模块是routes/index.js。一般情况下`/`路由对应的页面成为首页（或主页）。

```
/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});
```

把字符串"Express"修改为字符串"my first WebApp"，

在终端输入快捷组合键`ctrl + c`可以停止运行first-app服务。然后，再执行运行指令运行服务。

```
输入ctrl + c

$ DEBUG=first-app:* npm start
```

刷新浏览器页面，路由`/`对应的页面在显示上发生了相应的变化。

路由`/users`对应的逻辑模块是`/routes/users.js`。

```
/* GET users listing. */

router.get('/', function(req, res, next) {
  res.send('respond with a resource');
});
```

将“respond with a resource”修改为“zhangsan, lisi, wangermazi”，重新运行first-app再刷新页面看结果。

## 事例

参考事例 [first-app-sample-1](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-1)

