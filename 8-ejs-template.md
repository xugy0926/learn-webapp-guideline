# 模板引擎

在express中，模板引擎也可以称为视图引擎，随便怎么叫。express引入模板引擎的目的是为了更好地构建html页面。还记得在创建first-app项目时的参数吗？

```
$ express --view=ejs
```

用express创建项目是，后面跟了一个--view参数，它的值是ejs。这个意图很明确：在创建express项目时使用ejs作为模板引擎。

项目创建成功后，在.app.js中可以看到以下代码。

```js
// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');
```

express已经帮我们构建好了查找view的位置和解析view的引擎。当我们要返回页面时，只要告诉res.render函数返回的页面的名字即可。

```js
res.render('index', { title: 'Express' });
```

事实上，express只是应我们的要求，集成了ejs模块并封装了对ejs的处理。

ejs本身是一个express之外的第三方库，ejs的目的就是利用ejs模板文件和数据来构建html文件。比如上面的res.render函数其实是调用了ejs引擎，利用index.ejs模板文件和{ title: 'Express'}数据构建html文件，并返回给用户。

在使用模板引擎时，express封装了对ejs引擎的调用，在调用res.render函数返回页面是我们只关注两件事情。

1. 用哪个模板？
2. 给什么数据？

但，除了以上两件事情之外，在开发期间，我们还要来定制ejs模板。



