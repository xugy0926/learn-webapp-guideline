# 显示文章详情

文章列表和编辑文章都已经构建好，再补充文章详情显示即可把整个操作环路打通。

先设计一个显示文章详情的页面路由。

我们希望用户通过以下地址访问文章

```
localhost:3000/posts/show?id=123456
```

/posts/show是路由，query参数id是文章的id。

在./route.page.js中引入./models/post.js模块

```js
var PostModel = require('./models/post');
```

在./route.page.js中

```js
/* GET posts show page. */
router.get('/posts/show', function (req, res, next) {
  var id = req.query.id;

  PostModel.findOne({_id: id}, function (err, post) {
    res.render('show', {post});
  });
});
```

* 通过req.query.id可以读取url问号后面的id参数。
* 通过PostModel.findOne函数来查找\_id等于req.query.id的post。

新建show.ejs页面

```html
<!DOCTYPE html>
<html>
  <head>
    <title><%= post.title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
    <script src="https://cdn.bootcss.com/vue/2.4.4/vue.min.js"></script>
  </head>
  <body>
    <div id="app">
      <h1><%= post.title %></h1>
      <div><%= post.content %></div>
    </div>
  </body>
</html>
```

* 因为res.render\('show', {post}\)传入的数据是一个post对象，因此在show.ejs中解析数据时要用post.title和post.content来填充内容。

接下来就要构建从文章列表点击跳转到文章详情的过去，只需要在posts.ejs中把列表加上&lt;a&gt;跳转即可。

```
<div v-for="item in postsList">
  <a v-bind:href="item.url">{{ item.title }}</a>
</div>
```

在请求数据得到结果后，为每个item加上url字段

```javascript
<script>
  var vm = new Vue({
    el: '#app',
    data: {
      postsList: []
    }
  });

  axios.get('/api/posts')
    .then(function(response) {
      vm.postsList = response.data.postsList;
      vm.postsList.forEach((element) => element.url = '/posts/show?id=' + element._id);
    });
</script>
```

## 实例

参考 [first-app-sample-9](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-9)

