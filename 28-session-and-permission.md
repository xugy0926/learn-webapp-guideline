# Session 和功能控制

前面我们使用了 Cookie 来存储用户信息, Cookie 存在客户端，不是很安全，别人可以分析 Cookie 进行欺诈。除了使用 Cookie，我们还可以使用 Session 将用户信息存储在服务器端。

我们借助 mongodb 的存储来完成这个功能。

首先，需要安装两个第三方库。

```
$ npm install express-session
$ npm install connect-mongo
```

在配置文件中增加两个配置。

```js
// filepath: config.js

admin: 'admin',
sessionSecret: 'your_session_secret'
```

admin 用于在后面判断用户是否具备管理员权限，sessionSecret 用于存储用户信息的秘key值。

在 app.js 中引入上面两个库，并进行简单的设置。

```js
// filepath: app.js

var connectMongodb = require('connect-mongo');
var session = require('express-session');

...

var MongoStore = new connectMongodb(session);
var app = express();
```

在引入 auth 中间件的前面，引入 session 中间件，在 session 中间件里的 store 使用了前面构建的 MongoStore。这意味着用户信息都存储在 mongodb 中。

```js
app.use(
  session({
    secret: config.sessionSecret,
    store: new MongoStore({
      url: config.mongodbUrl
    }),
    resave: true,
    saveUninitialized: true
  })
);

app.use(auth.authUser);
```

改造 authUser 函数。
之前，该函数用于与对 Cookie 信息读取并找到对应的用户信息。
现在，我们增加了一个分支的判断，即先对 req.session 进行判断，如果 req.session里有 user 信息，那么就用这个，并赋值给 res.locals.currentUser。

注：res.locals.currentUser 这个对象是给 ejs 构建页面时使用的。

```js
//filepath: ./middlewares/auth.js

function authUser(req, res, next) {
  res.locals.currentUser = null;

  if (req.session && req.session.user) {
    const user = req.session.user;
    res.locals.currentUser = user;
    next();
  } else {
    const authToken = req.signedCookies[config.cookieName] || '';

    if (authToken) {
      UserModel.findOne({ _id: authToken }, function(err, user) {
        if (err || !user) {
          next();
        } else {
          if (user.loginname === config.admin) {
            user.isAdmin = true;
          }

          req.session.user = user;
          res.locals.currentUser = user;
          next();
        }
      });
    } else {
      next();
    }
  }
}
```

上面代码中在处理 Cookie 分支那段代码，增加了一个是否为管理员账号的判断，我们假定 config 中的 admin 就是管理员账户。并动态的对 user 对象添加一个 isAdmin 并赋值为 true。

注意：user 要不仅要给 res.locals.currentUser，而且要给 req.session。

```js
//filepath: ./middlewares/auth.js auth

if (user.loginname === config.admin) {
  user.isAdmin = true;
}

req.session.user = user;
res.locals.currentUser = user;
next();
```

添加一个管理员权限控制的中间件

```js
//filepath: ./middlewares/auth.js

function adminRequired(req, res, next) {
  if (!req.session || !req.session.user) {
    let err = new Error('需要登录');
    err.status = 403;
    next(err);
    return;
  }

  if (!req.session.user.isAdmin) {
    let err = new Error('需要管理员权限');
    err.status = 403;
    next(err);
    return;
  }

  next();
}
```

现在既可以把用户信息存在 session 里，也拥有了一个判断是否为管理员权限的中间件。

我们只需要把 adminRequired 放在恰当的位置即可。

比如只有管理员权限的用户才能打开新建文章页面。

```js
// filepath: route.page.js

var auth = require('./middlewares/auth');
/* GET posts create page. */
router.get('/posts/create', auth.adminRequired, function(req, res, next) {
  res.render('create');
});
```

## 实例

参考 [first-app-sample-20](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-20)

