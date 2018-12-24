# 模板引擎

在 express 中，模板引擎也可以称为视图引擎，随便怎么叫。express 引入模板引擎的目的是为了更好地构建 html 页面。还记得在创建 first-app 项目时的参数吗？

```
$ express --view=ejs
```

用 express 创建项目时，后面跟了一个--view 参数，它的值是 ejs。这个意图很明确：在创建 express 项目时使用 ejs 作为模板引擎。

项目创建成功后，在.app.js 中可以看到以下代码。

```javascript
// view engine setup
app.set("views", path.join(__dirname, "views"));
app.set("view engine", "ejs");
```

express 已经帮我们构建好了查找 views 的位置和解析 views 的引擎。当我们要返回页面时，只要告诉 res.render 函数返回的页面的名字即可。

```javascript
res.render("index", { title: "Express" });
```

事实上，express 只是应我们的要求，集成了 ejs 模块并封装了对 ejs 的处理。

ejs 本身是一个 express 之外的第三方库，ejs 的目的就是利用 ejs 模板文件和数据来构建 html 文件。比如上面的 res.render 函数其实是调用了 ejs 引擎，利用 index.ejs 模板文件和{ title: 'Express'}数据构建 html 文件，并返回给用户。

在使用模板引擎时，express 封装了对 ejs 引擎的调用，在调用 res.render 函数返回页面时我们只关注两件事情。

1. 用哪个模板？
2. 给什么数据？

但，除了以上两件事情之外，在开发期间，我们还要会定制 ejs 模板。
