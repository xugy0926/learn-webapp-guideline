# 路由归类

WebApp不仅可以提供页面能力，还能提供数据。

访问localhost:3000/posts可以打开页面，访问localhost:3000/posts/list可以获取文章数据。当处理的路由很多的情况下，管理路由就会成为灾难。

想一下，如果不断增加first-app的页面，在.app.js文中下面的代码将会不断的膨胀。

```js
app.use('/', index);
app.use('/users', users);
app.use('/posts', posts);
```

在路由控制上应该加以区分才能保证first-app的结构更清晰更易维护。

在设计WebApp时把提供数据的路由称为接口，一般都带有api。

##### 页面路由

```js
localhost:3000/      // 获取主页
localhost:3000/users // 获取users页面
localhost:3000/posts // 获取posts页面
```

##### 数据路由

```js
localhost:3000/api/posts  // 获取posts列表
```

以上的分类会帮助我们更好的维护对路由的处理，然后只需要两个路由处理文件即可。

新建route.page.js和route.api.js两个文件

* route.page.js

```js
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

/* GET posts page. */
router.get('/posts', function(req, res, next) {
  res.render('posts', { title: 'posts'} );
});

module.exports = router;
```

* route.api.js

```js
var express = require('express');
var router = express.Router();

/* GET users lists. */
router.get('/users', function(req, res, next) {
  res.send('respond with a resource');
});

/* GET posts lists */
router.get('/posts', function(req, res, next) {
  res.json({postsList: ['文章1', '文章2', '文章3']});
});

module.exports = router;
```

修改app.js的路由和处理文件的对应关系

```js
var page = require('./route.page');
var api = require('./route.api');

...

app.use('/', page);
app.use('/api', api);
```

因为把'/posts/list'路由改成了'/api/posts'，所以./views/posts.ejs中请求posts数据的url也要修改。

```js
fetchData () {
  axios.get('/api/posts')
    .then(function(response) {
      vm.postsList = response.data.postsList;
    })
}
```

通过以上的操作我们把路由重新归类整理了一番，以保证后续能更好的开发功能。

## 实例

参考 [first-app-sample-6](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-6)

