# 处理HTTP POST请求

前面都是处理GET请求，通过GET请求获取页面和数据。处理POST请求也是WebApp的能力之一。通过页面输入数据并发送到WebApp的处理过程是怎么样的呢？大致过程分以下过程。

1. 设计一个路由对应一个编辑页面。
2. 用户通过路由打开页面后，输入内容，并发送HTTP POST请求把数据给WebApp。
3. WebApp收到请求，依据路由分发到对应的处理模块。
4. 处理模块获取用户的数据并进行相应处理。

新建一个路由返回新建文章的页面

在route.page.js增加路由'/posts/create'的处理

```javascript
/* GET posts edit page. */
router.get('/posts/create', function(req, res, next) {
  res.render('create');
});
```

新建一个编辑页面create.ejs

```html
<!DOCTYPE html>
<html>
  <head>
    <link rel='stylesheet' href='/stylesheets/style.css' />
    <link href="https://cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.bootcss.com/vue/2.4.4/vue.min.js"></script>
    <script src="https://cdn.bootcss.com/axios/0.16.2/axios.min.js"></script>
  </head>
  <body>
    <div id="app">
      <h1>新建文章</h1>
      <div class="form-group">
        <input type="text" class="form-control" v-model="title" placeholder="输入文字标题">
      </div>
      <div class="form-group">
        <textarea class="form-control" rows="3" v-model="content" placeholder="输入文章内容"></textarea>
      </div>
      <div class="form-group">
        <button class="btn btn-default" v-on:click="submit">提交</button>
      </div>
    </div>
  </body>
</html>
<script>
var vm = new Vue({
  el: '#app',
  data: {
    title: '',
    content: ''
  },
  methods: {
    submit () {
    axios.post('/api/posts',
      {
        title: vm.title,
        content: vm.content
      })
      .then(function(response) {
        alert(JSON.stringify(response.data));
      })
      .catch(function(err) {
        alert(err);
      })
    }
  }
});
</script>
```

在这个编辑页面利用vue技术将&lt;input&gt;和&lt;textarea&gt;标签输入的内容和data中的title和content变量进行绑定，提交按钮事件绑定到methods的submit方法。

axios采用post方法来发送数据，路由地址是'/api/posts'。

在route.api.js中增加路由'api/posts'的post请求处理。

```javascript
/* POST posts */
router.post('/posts', function (req, res, next) {
  var title = req.body.title;
  var content = req.body.content;
  res.send({title, content}); // 收到数据后，又把数据返回给了请求方
});
```

至此，我们完整构建好了从输入到接受数据的整个流程。

值得注意的一点。

```js
/* GET posts lists */
router.get('/posts', function(req, res, next) {
res.json({postsList: ['文章1', '文章2', '文章3']});
})

/* POST posts */
router.post('/posts', function (req, res, next) {
  var title = req.body.title;
  var content = req.body.content;
  res.send({title, content}); // 收到数据后，又把数据返回给了请求方
});
```

以上两个路由地址都是'api/posts'，但HTTP的请求方法不同，GET请求的'api/posts'会分发给router.get，而POST请求的'api/posts'会分发给router.post。

事实上完全可以设计不同的路由地址加以区分，但没有用HTTP的请求方法区分方便。

## 实例

参考 [first-app-sample-7](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-7)

