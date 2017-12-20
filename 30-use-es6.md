# 使用 es6 语法

#### 引入第三方库

```js
var express = require('express');
```

改用 import 来导出

```js
import express from 'express';
```

#### 引入自定义模块

```js
var config = require('./config');
```

一样用 import 来导入

```js
import config from './config';
```

#### 修改导出模块的方式

```js
// ./src/config.js

module.exports = {
  cookieName: 'your_cookie_name',
  mongodbUrl: 'mongodb://localhost:27017/firstapp',
  admin: 'admin',
  sessionSecret: 'your_session_secret'
}
```

可以通过 export 方式导出模块，如果你的模块只导出一个对象，就直接用 export default 

```js
// ./src/config.js

export default {
  cookieName: 'your_cookie_name',
  mongodbUrl: 'mongodb://localhost:27017/firstapp',
  admin: 'admin',
  sessionSecret: 'your_session_secret'
}
```

#### 导出多个函数（对象）

```js
// ./src/middlewares.js

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
        if (err) {
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

module.exports = { authUser, adminRequired };
```

可以用 export 挨个导出函数变量，这样就不用在文件最后多余做导出（比如 `module.exports = { authUser, adminRequired }`）

```js
// ./src/middlewares.js

export const authUser = (req, res, next) => {
  res.locals.currentUser = null;

  if (req.session && req.session.user) {
    const user = req.session.user;
    res.locals.currentUser = user;
    next();
  } else {
    const authToken = req.signedCookies[config.cookieName] || '';

    if (authToken) {
      UserModel.findOne({ _id: authToken }, function(err, user) {
        if (err) {
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

export const adminRequired = (req, res, next) => {
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

通过 export 导出某个文件的多个函数（对象），需要用下面的方式来 import。
```js
import * as auth from './middlewares/auth';
```

#### 能用 const 就不要用 var，即使不能用 const，也不能用 var，而要用 let。

```js
var MongoStore = new connectMongodb(session);
var app = express();
```

我们并不会直接修改 MongoStore 和 app 这两个变量，所以要用 const 来限制恶意的修改动作。

```js
const MongoStore = new connectMongodb(session);
const app = express();
```

#### 用解构，而不是挨个读取对象的属性

```js
var id = req.params.id;
var title = req.params.title;
var content = req.params.content;
```

```js
const { id, title, content } = req.params;
```

## 实例

参考 [first-app-sample-22](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-22)