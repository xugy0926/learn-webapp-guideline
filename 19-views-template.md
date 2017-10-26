# 页面模板

以下是前面构建的所有页面。

页面|说明
---|---
views/create.ejs | 新建文章页面
views/edit.ejs | 编辑文章页面
views/index.ejs | 主页面
views/posts.ejs | 文章列表页面
views/show.ejs | 显示文章页面

这些页面都是独立的html页面，随着业务的复杂，不断的添加页面，意味着要不断的新建页面。有没有发现一个问题，每个页面很多内容是重复的，比如`<html><head><body>`。

一定要想出一个办法构建一个公共的页面，公共页面包涵了html的主结构`<html><head><body>`。其他各个页面的主要内容嵌入到里面即可。

（TODO：这里要补一张图）

## 引入express-ejs-layouts

#### 安装

```
$ npm install --save express-ejs-layouts
```

#### 引入

在app.js中引入express-ejs-layouts

```
var expressLayouts = require('express-ejs-layouts');
```

#### 使用

在 app.js 设置完view engine后，使用 expressLayouts

```js
// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');
app.use(expressLayouts);
```

#### 创建全局模板

新建 views/layout.ejs。

```html
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css">
    <link rel="stylesheet" href='/stylesheets/style.css' />

    <script src="https://cdn.bootcss.com/jquery/2.2.4/jquery.min.js"></script>
    <script src="https://cdn.bootcss.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
    <script src="https://cdn.bootcss.com/vue/2.4.4/vue.min.js"></script>
    <script src="https://cdn.bootcss.com/axios/0.16.2/axios.min.js"></script>
  </head>
  <body>
    <div id="app">
      <div class="container">
        <%- body %>
      </div>
    </div>
  </body>
</html>
```

#### 修改其余页面

比如在 views/index.ejs 中，可以去掉多余的标签，只关心这个页面核心内容。

```html
<div class="col-md-8 col-md-offset-2">
  <div class="jumbotron">
    <h1>Hello, world!</h1>
    <p>这是我记忆空间。</p>
    <p><a class="btn btn-primary btn-lg" href="/posts" role="button">进入新世界</a></p>
  </div>
</div>
```

其余页面的修改类似和 views/index.ejs 类似。

## 事例

参考事例 [first-app-sample-13](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-13)

