# 定制ejs模板

我们拿posts.ejs为例，如何在posts.ejs中构建一个列表结构的html呢？

比如，在posts.js中我们给定一个列表postsList

```
router.get('/', function(req, res, next) {
  res.render('posts', { title: 'posts', postsList: ['文章1', '文章2', '文章3'] });
});
```

在posts.ejs中，应该利用数据postsList来构建列表模板。

```
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1><%= title %></h1>
    <p>Welcome to <%= title %></p>

    <% for(var i = 0; i < postsList.length; i++) { %>
    <p><%= postsList[i] %></p>
    <% } %>
  </body>
</html>
```

ejs模板文件中可以嵌入代码，但代码一定是写在&lt;% %&gt;中。下面归纳一些使用技巧。

使用某个变量的值要用&lt;%= %&gt;

```
<title><%= title %></title>
```

变量可以再计算

```
<title><%= title + ' page' %></title>
```

执行某段代码用&lt;% %&gt;

```
<% for(var i = 0; i < postsList.length; i++) { %>
<p><%= postsList[i] %></p>
<% } %>
```

总之，只需要在正常的代码前后加上&lt;% 和 %&gt;即可，如果是赋值语句还要加上=号。

ejs模板引擎针对学习者相对很友好，只是看上去有点乱而已。

## 事例

参考事例 [first-app-sample-4](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-4)

