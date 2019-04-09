# 延迟 html 渲染时机(客户端渲染数据)

./routes/posts.js 里调用 res.render 函数处理用户的请求时，提供数据和 ejs 模板并最终交给了 ejs 引擎去构建 html 页面，然后再把页面返回给发出请求的用户。

这种在 WebApp 里依据数据构建好 html 的技术称为服务器构建技术，服务器会一起处理得妥妥的，浏览器获得 html 页面后只需要渲染 html 页面即可。

除了服务器构建技术，还可以把依据数据构建 html 的过程延迟到浏览器去进行，大致处理步骤如下。

1. 通过/posts 路由，获得对应的静态页面。
2. 浏览器解析完页面后，通过 axios 向服务器发起 http 请求以获得文章数据。
3. 拿到数据后通过 vue 来重新渲染页面。

为了满足这个过程，必须创建一个获取文章列表的路由。

在./routes/posts.js 中添加一个/posts/list 的路由处理

```js
/* GET 获取postsList数据 */
router.get("/list", function(req, res, next) {
  res.json({ postsList: ["文章1", "文章2", "文章3"] });
});
```

注意，这个路由通过 res.json\(\)函数返回一个对象数据。res.json 可以将一个 json 对象返回给请求方。

在./views/posts.ejs 的 head 部分添加对 vue 和 axios 的引用

```html
<head>
  <title><%= title %></title>
  <link rel="stylesheet" href="/stylesheets/style.css" />
  <script src="https://cdn.bootcss.com/vue/2.4.4/vue.min.js"></script>
  <script src="https://cdn.bootcss.com/axios/0.16.2/axios.min.js"></script>
</head>
```

在./views/posts.ejs 中添加&lt;script&gt;代码来获得 postsList 并更新页面。

```html
<script>
  var vm = new Vue({
    el: "#app",
    data: {
      postsList: []
    },
    methods: {
      fetchData() {
        axios.get("/posts/list").then(function(response) {
          vm.postsList = response.data.postsList;
        });
      }
    }
  });

  vm.fetchData();
</script>
```

在./views/posts.ejs 中修改&lt;body&gt;代码;这块代码会在浏览器加载完 html 后才会执行，所以获取 postsList 被延迟到浏览器才执行，然后再利用 vue 更新 html 的机制来刷新页面。

```html
<body>
  <div id="app">
    <h1><%= title %></h1>
    <p>Welcome to <%= title %></p>

    <p v-for="item in postsList">{{ item }}</p>
  </div>
</body>
```

延迟 html 渲染时机这种技术称为客户端渲染。

## 实例

参考 [first-app-sample-5](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-5)
