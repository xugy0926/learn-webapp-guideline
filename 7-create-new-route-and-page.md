# 添加新的路由和页面

一个路由对应一个页面或一个操作，学习如何添加路由和页面是开发WebApp的最基本的能力。

假如想让localhost:3000/posts这个地址打开一个对应的页面。一般都按照以下顺序来操作。

1. 添加一个处理/posts路由的文件。如：./routes/posts.js。
2. 添加一个/posts路由对应的页面文件。如./views/posts.ejs。
3. 在app.js中将/posts和./routes/posts.js进行关联。
4. 在./routes/posts.js中把./views/posts.ejs页面返回给用户。

## 新建./routes/posts.js

./routes/posts.js用来处理路由逻辑，在这里你可以决定路由/posts是返回数据还是页面。

## 新建./views/posts.ejs

./views/posts.ejs是用来构建用户看到的html页面。

## 编写./routes/posts.js

只需要把./routes/index.js中的代码copy到./routes/posts.js即可。但是要修改两处内容。

##### 第一处：修改注释。

/routes/posts.js是处理路由’/posts‘的逻辑，注释中的Get Home page要修改为Get posts page。这样就保证注释的正确性。当别人看你的代码时，就不会产生误会。

##### 第二处：修改返回的页面

在./routes/index.js中，res.render\(\)函数的第一个参数是’index‘，这个’index‘对应着/views/index.ejs文件。在新建的./routes/posts.js文件中，将res.render\(\)函数的第一个参数'index'修改为'posts'。

## 编写views/posts.ejs的页面代码

把/views/index.ejs的代码copy到/views/posts.ejs文件。

这样重新运行项目，在浏览器输入localhost:3000/posts

## 关联路由'/posts'和'/routes/posts.js'

我们可以顺一下处理逻辑。

* 当浏览器输入地址localhost:3000/，最终代码会执行到routes/index.js中。

* 在routes/index.js中，会把views/index.ejs生产的html页面返回给用户。

以上操作会产生两个疑惑。

1. 程序处理’/‘时，是怎么找到./routes/index.js的？这两者的关联性在哪里？如果知道关联性就能知道如何关联'/posts'

2. routes/index.js中的res.render\(\)函数，为什么会把第一个参数‘index’认为是views/index.ejs？关联性在哪里？

在app.js中，引入./routes/posts.js文件，并赋值给变量posts。

```
var posts = require('./routes/posts');
```

然后，将变量posts和路由'/posts'关联起来。

```
app.use('/posts', posts);
```

require\(\)是nodejs的一个全局函数，专门用于引用内部文件（模块）。一个项目为了保证功能模块更清晰，会将不同的功能写在不同的文件里。比如，./app.js控制路由，./routes/index.js处理某个路由逻辑，当./app.js要调用./routes/index.js时，就必须先将./routes/index.js引入进来。并用赋值给变量index。

```
var index = require('./routes/index');
var posts = require('./routes/posts');
```

所以，要引入./routes/posts.js文件也是一样的处理。

注意，require\('./routes/posts.js'\)和require\('./routes/posts'\)是等价的。文件的后缀`.js`是可以不写的，写了也不会错。

将一个文件（模块）引入进来后就得使用，否则，引入一个文件（模块）就显得无意义。

app.use\(\)函数可以接受两个参数，第一个参数表示路由，第二个参数表示该路由对应的处理模块。

```
app.use('/posts', posts);
```

通过app.use函数把路由/posts和逻辑处理./routes/posts.js文件关联起来了，整个代码就能走得通。

## 如何让./routes/posts.js返回./views/posts.ejs这个页面

在./routes/posts.js中，res.render\(\)的第一个参数是'index'，如果把'index'直接改成'posts'是不是就可以了？答案是可以的。那为什么我输入'posts'就能关联到'./views/posts.ejs'这个文件呢？

奥秘是在于在./app.js里，项目已经实现把./views这个路径告诉了app。

```
app.set('views', path.join(__dirname, 'views'));
```

app.set\(\)函数可以把一个值设置给app，这行代码的意思就是把./views这个路径设置给'views'这个字符串对应的值。那么，在代码执行时，app只要取出字符串'views'对应的路径即可。

所以，app是预先就知道了如果要找view相关的文件，就去./views的路径下找即可。现在，只需要把./routes/posts.js中的res.render\(\)函数的第一个参数'index'改成'posts'即可。

#### 6. 预览结果

重新运行first-app，输入localhost:3000/posts，页面和localhost:3000/一样了就说明成功为first-app添加了一个新的访问路由/posts。

## 实例

参考 [first-app-sample-3](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-3)

