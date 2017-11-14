# 添加导航条

导航条是一个页面最重要的模块，bootstrap3.0 提供了很方便的导航条解决方案。

http://v3.bootcss.com/components/#navbar

首先在views里新建一个_nav.ejs

```html
<nav class="navbar navbar-default">
  <div class="container-fluid">
    <!-- Brand and toggle get grouped for better mobile display -->
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="/">我的世界</a>
    </div>

    <!-- Collect the nav links, forms, and other content for toggling -->
    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
      <ul class="nav navbar-nav">
        <li><a href="/posts">文章</a></li>
      </ul>
    </div>
  </div>
</nav>
```

然后在 views/layout.ejs 中加入对 _nav.ejs 的引入

```html
<div class="container">
  <% include ./_nav %>
  <%- body %>
</div>
```

这里有3个注意点：
1. 将导航条的html放入在 _nav.ejs 是为了更好的页面模块分类。
2. 命名用下划线_ 开始，我是想强调这个ejs是供其他页面引用，而不是一个独立页面，这样可以将内部模块（组件）和独立页面分开。
3. 在页面中引入其他的模块用 <% include file_path %>，include关键字和c语言中引入库的关键字一样。

## 实例

参考 [first-app-sample-16](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-16)