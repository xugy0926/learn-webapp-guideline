# 添加新的路由和页面

一个路由对应一个页面或一个操作，学习如何添加路由和页面是开发 WebApp 的最基本的能力。

假如想让 localhost:3000/posts 这个地址打开一个对应的页面。一般都按照以下顺序来操作。

1. 添加一个处理/posts 路由的文件。如：./routes/posts.js。
1. 添加一个/posts 路由对应的页面文件。如./views/posts.ejs。
1. 在 app.js 中将/posts 和./routes/posts.js 进行关联。
1. 在./routes/posts.js 中把./views/posts.ejs 页面返回给用户。

## 新建./routes/posts.js

./routes/posts.js 用来处理路由逻辑，在这里你可以决定路由/posts 是返回数据还是页面。

## 新建./views/posts.ejs

./views/posts.ejs 是用来构建用户看到的 html 页面。

## 编写./routes/posts.js

只需要把./routes/index.js 中的代码 copy 到./routes/posts.js 即可。但是要修改两处内容。

##### 第一处：修改注释。

/routes/posts.js 是处理路由’/posts‘的逻辑，注释中的 Get home page 要修改为 Get posts page。这样就保证注释的正确性。当别人看你的代码时，就不会产生误会。

##### 第二处：修改返回的页面

在./routes/index.js 中，res.render\(\)函数的第一个参数是’index‘，这个’index‘对应着/views/index.ejs 文件。在新建的./routes/posts.js 文件中，将 res.render\(\)函数的第一个参数'index'修改为'posts'。

## 编写 views/posts.ejs 的页面代码

把/views/index.ejs 的代码 copy 到/views/posts.ejs 文件。

这样重新运行项目，在浏览器输入 localhost:3000/posts

## 关联路由'/posts'和'/routes/posts.js'

我们可以顺一下处理逻辑。

- 当浏览器输入地址 localhost:3000/，最终代码会执行到 routes/index.js 中。

- 在 routes/index.js 中，会把 views/index.ejs 生产的 html 页面返回给用户。

以上操作会产生两个疑惑。

1. 程序处理’/‘时，是怎么找到./routes/index.js 的？这两者的关联性在哪里？如果知道关联性就能知道如何关联'/posts'

1. routes/index.js 中的 res.render\(\)函数，为什么会把第一个参数‘index’认为是 views/index.ejs？关联性在哪里？

在 app.js 中，引入./routes/posts.js 文件，并赋值给变量 posts。

```javascript
var posts = require("./routes/posts");
```

然后，将变量 posts 和路由'/posts'关联起来。

```javascript
app.use("/posts", posts);
```

require\(\)是 node.js 的一个全局函数，专门用于引用内部文件（模块）。一个项目为了保证功能模块更清晰，会将不同的功能写在不同的文件里。比如，./app.js 控制路由，./routes/index.js 处理某个路由逻辑，当./app.js 要调用./routes/index.js 时，就必须先将./routes/index.js 引入进来，并赋值给变量 index。

```javascript
var index = require('./routes/index');
var posts = require('./routes/posts');
```

所以，要引入./routes/posts.js 文件也是一样的处理。

注意，require\('./routes/posts.js'\)和 require\('./routes/posts'\)是等价的。文件的后缀`.js`是可以不写的，写了也不会错。

将一个文件（模块）引入进来后就得使用，否则，引入一个文件（模块）就显得无意义。

app.use\(\)函数可以接受两个参数，第一个参数表示路由，第二个参数表示该路由对应的处理模块。

```javascript
app.use("/posts", posts);
```

通过 app.use 函数把路由/posts 和逻辑处理./routes/posts.js 文件关联起来了，整个代码就能走得通。

## 如何让./routes/posts.js 返回./views/posts.ejs 这个页面

在./routes/posts.js 中，res.render\(\)的第一个参数是'index'，如果把'index'直接改成'posts'是不是就可以了？答案是可以的。那为什么我输入'posts'就能关联到'./views/posts.ejs'这个文件呢？

奥秘在./app.js 里，项目已经事先把./views 这个路径告诉给 app。

```javascript
app.set("views", path.join(__dirname, "views"));
```

app.set\(\)函数可以把一个值设置给 app，这行代码的意思就是把./views 这个路径设置给'views'这个字符串对应的值。那么，在代码执行时，app 只要取出字符串'views'对应的路径即可。

所以，app 是预先就知道了如果要找 views 相关的文件，就去./views 的路径下找即可。现在，只需要把./routes/posts.js 中的 res.render\(\)函数的第一个参数'index'改成'posts'即可。

#### 6. 预览结果

重新运行 first-app，输入 localhost:3000/posts，页面和 localhost:3000/一样了就说明成功为 first-app 添加了一个新的访问路由/posts。

## 实例

参考 [first-app-sample-3](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-3)
