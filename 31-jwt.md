# JWT的运用

JWT 的全称是 Json web token，JWT 一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，常用于分布式焊点的单点登录（SSO）场景。该 token 更加的紧凑并且很安全。

前面我们用过 cookie 和 session 来保存用户信息。

#### cookie

- 用户信息存在浏览器的 cookie 里，当浏览器发起请求时，会把 cookie 信息一并带给服务器。
- 服务器解开 cookie 信息后，会依据 cookie的信息查询是否为真实用户发来的请求。
- cookie 保存的信息是有限制的。

#### session

- 登录用户信息保存在服务器端（可以保存在内存、数据库或文件）。
- session 的唯一标识要依靠 cookie 保存在客户端，当客户端发起请求时，会把 cookie 给服务器，服务器会解开 cookie 并获得 session 的唯一标识，以后这个唯一标识去查找对应的数据。
- session 不仅可以记录用户的信息，也可以记录业务信息。更多时候是用 session 来跟踪用户的状态。

#### jwt
- jwt 很单纯，仅仅处理用户身份信息。
- 服务器将用户信息加密生成 jwt 字符串后发给客户端。
- 客户端每次发请求时将获得的 jwt 字符串一并给到 服务器。
- 客户端（iOS，android）一般把 jwt 字符串设置到 http 请求的 header 部分，但是，在进行网页开发时，可以依靠 cookie 来存放。

![](https://ws3.sinaimg.cn/large/006tKfTcly1fmt65ppd12j31120ksac4.jpg)

#### 安装依赖

```
$ npm install jwt-simple
$ npm install moment
```

#### 添加配置

在配置文件中，添加 jwtSecret。

```js
// filepath: ./src/config.js

export default {
  cookieName: 'your_cookie_name',
  jwtSecret: 'your_jwt_secret',
  mongodbUrl: 'mongodb://localhost:27017/firstapp',
  admin: 'admin',
  sessionSecret: 'your_session_secret'
}
```

#### 修改登录过程

在处理登录时，将 `_id` `name` `isAdmin` `exp(过期时间)` 通过 jwt 编码成一个字符串，以后称该字符串为 token。

并将 token 保存在 cookie 里，这样客户端在发起 http 请求时才有机会把 token 送给服务器。

```js
// filepath: ./src/route.api.js

/* POST signin user */
router.post('/signin', function(req, res, next) {
  const { name, pass } = req.body;

  UserModel.findOne({ name }, function(err, user) {
    if (err || !user) {
      return next(new Error('找不到用户'));
    } else {
      const isOk = bcrypt.compareSync(pass, user.pass);
      if (!isOk) {
        return next(new Error('密码不对'));
      }
      
      // 生产 token
      const token = jwt.encode(
        {
          _id: user._id,
          name: user.name,
          isAdmin: user.name === config.admin ? true : false,
          exp: moment().add('days', 30).valueOf(),
        },
        config.jwtSecret
      );

      const opts = {
        path: '/',
        maxAge: moment().add('days', 30).valueOf(),
        signed: true,
        httpOnly: true
      };

      // 将 token 保存在 cookie 里。
      res.cookie(config.cookieName, token, opts);
      res.json({ token });
    }
  });
});
```

注意，最后我用了 res.json({ token }) 把 token 发送给客户端。这样做的原因是客户端有机会单独对 token 进行相应的处理，比如：如果是 android 客户端，它并没有 cookie 机制，那么它可以把 token 单独存在某地，供以后发起请求时用。

#### 修改验证登录的中间件

服务器要获得 token，需要解开 cookie 并拿到 token。然后对 token 进行解码，并获得里面的信息。

因为我们在构成 token 时，里面有 `_id`  `name` `isAdmin`信息，那么我们就不需要重新去数据库里查找用户，这样节约了一次查询动作，整个处理速度更快。

在下面的代码中，在读取 cookie 中的 token 之前，会先检查一下请求的 headers 部分是否也有 token，如果有，就用 headers 里的。这样做的目的是为了照顾那些不用 cookie 方式传 token的客户端。比如，android 客户端传递 token 一般会往请求的 headers 设置 token。

以下的代码就兼容了两种传递 token 的方式。

```js
// filepath: ./src/middlewares/auth.js

export const authUser = (req, res, next) => {
  res.locals.currentUser = null;

  const token =
    req.headers['x-access-token'] || req.signedCookies[config.cookieName] || '';

  if (token) {
    try {
      const decoded = jwt.decode(token, config.jwtSecret);
      if (decoded.exp <= Date.now()) {
        res.end('Access token has expired', 400);
        return;
      }

      req.user = res.locals.currentUser = decoded;
      return next();
    } catch (err) {
      return next();
    }
  } else {
    next();
  }
};
```

后续判断用户是否登录就可以用 req.user，而不用 req.session.user

```js
export const userRequired = (req, res, next) => {
  if (!req.user) {
    let err = new Error('需要登录');
    err.status = 403;
    next(err);
    return;
  }

  next();
};

export const adminRequired = (req, res, next) => {
  if (!req.user) {
    let err = new Error('需要登录');
    err.status = 403;
    next(err);
    return;
  }

  if (!req.user.isAdmin) {
    let err = new Error('需要管理员权限');
    err.status = 403;
    next(err);
    return;
  }

  next();
};
```

#### 去掉 session 处理

最后我们把 session 的处理去掉，已经不需要用它来记录用户信息了。

删除下面代码。

```js
// filepath: ./src/app.js

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
```

## 实例

参考 [first-app-sample-23](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-23)
