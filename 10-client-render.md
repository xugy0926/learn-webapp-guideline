# 延迟html渲染时机(客户端渲染数据)

./routes/posts.js里调用res.render函数处理用户的请求时，提供数据和ejs模板并最终交给了ejs引擎去构建html页面，然后再把页面返回给发出请求的用户。

这种在WebApp里依据数据构建好html的技术成为服务器构建技术，服务器会一起处理得妥妥的，浏览器获得html页面后只需要渲染html页面即可。

除了服务器构建技术，还可以把依据数据构建html的过程延迟到浏览器去进行，大致处理步骤如下。

1. 通过/posts路由，获得对应的静态页面。
2. 浏览器解析完页面后，通过axios向服务器发起http请求以获得文章数据。
3. 拿到数据后通过vue来重新渲染页面。

为了满足这个过程，必须创建一个获取文章列表的路由。

在./routes/posts.js中添加一个/posts/list的路由处理

```
/* GET 获取postsList数据 */
router.get('/list', function(req, res, next) {
  res.json({postsList: ['文章1', '文章2', '文章3'] });
});
```

注意，这个路由通过res.json\(\)函数返回一个对象数据。res.json可以将一个json对象返回给请求方。

在./views/posts.ejs的head部分添加对vue和axios的引用

```
<head>
  <title><%= title %></title>
  <link rel='stylesheet' href='/stylesheets/style.css' />
  <script src="https://cdn.bootcss.com/vue/2.4.4/vue.min.js"></script>
  <script src="https://cdn.bootcss.com/axios/0.16.2/axios.min.js"></script>
</head>
```

在./views/posts.ejs中添加&lt;script&gt;代码来获得postsList并更新页面。

```
<script>
  var vm = new Vue({
    el: '#app',
    data: {
      postsList: []
    },
    methods: {
      fetchData () {
        axios.get('/posts/list')
          .then(function(response) {
            vm.postsList = response.data.postsList;
          })
      }
    }
  });

  vm.fetchData();
</script>
```

这块代码会在浏览器加载完html后才会执行，所以获取postsList被延迟到浏览器才执行，然后再利用vue更新html的机制来刷新页面。

```
<body>
  <div id="app">
    <h1><%= title %></h1>
    <p>Welcome to <%= title %></p>

    <p v-for="item in postsList">{{ item }}</p>
  </div>
</body>
```

延迟html渲染时机这种技术成为客户端渲染。

## 事例

参考事例 [first-app-sample-5](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-5)

