# 账号系统

在webapp中，账号信息可以记录在浏览器里的cookie模块，浏览器会根据不同的域名来分配一块cookie空间。

当用户在页面输入账号和密码后，webapp验证登录信息是正确的，这时服务会把账户信息塞到请求的cookie中并返回给客户端，客户端拿到信息后，把账户信息存在浏览器的cookie中。

下一次客户端向服务发起请求时，浏览器会帮我们自动把cookie附加到请求里，以供服务来判断这是一个已经登录的用户。当服务收到请求后，第一件事情应该是分析cookie里的信息并判断这个用户是不是一个真实的用户。

#### 一次登录过程

![](/assets/login.png)

在登录时，服务判断账户和密码准确后，会把相关用户信息放在 response 里 cookie 体内。

当服务响应登录请求后，客户端会收到 response 里的 cookie 信息并存在浏览器的 cookie模块里。

#### 一次请求过程

![](/assets/request-cookie.png)

每次往服务发http请求时，浏览器里的cookie都会被默认带上，服务器收到请求第一件事就是分析cookie信息并判断该用户是否存在，如果存在就认为该用户是登录的。

虽然cookie最终存在浏览器里，但cookie信息不能人为串改的。写入到浏览器的cookie信息和分析cookie都是由服务来进行。

#### 添加存储用户信息的表

新建 models/user.js

```js
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var UserSchema = new Schema({
    name: String,
    pass: String
});

const UserModel = mongoose.model('User', UserSchema);

module.exports = UserModel;
```

上面只存了两个字段：名字和密码。

#### 定义一个全局的配置文件

新建 config.js

```js
module.exports = {
  cookieName: 'your_cookie_name'
}
```

存在cookie里的信息一定需要一个名字。

为了更好的管理，设计一个 config.js 来管理 webapp 中更多的静态信息。比如：后面可以把数据库的url在这里定义。

#### 增加登录和注册页面

1. 在 route.page.js 中添加新的页面路由处理

```js
/* GET signup page. */
router.get('/signup', function(req, res, next) {
  res.render('signup');
});

/* GET signin page. */
router.get('/signin', function (req, res, next) {
  res.render('signin');
});
```

2. 新建登录页面 views/signin.ejs 。

```html
<div class="col-md-4 col-md-offset-4">
  <h1>登录</h1>
  <div class="form-group">
    <input type="text" class="form-control" v-model="name" placeholder="账号">
  </div>
  <div class="form-group">
    <input type="password" class="form-control" v-model="pass" placeholder="密码">
  </div>
  <div class="form-group">
    <button class="btn btn-default" v-on:click="submit">提交</button>
  </div>
</div>
<script>

var vm = new Vue({
  el: '#app',
  data: {
    name: '',
    pass: ''
  },
  methods: {
    submit () {
      axios.post('/api/v1/signin',
        {
          name: vm.name,
          pass: vm.pass
        })
        .then(function(response) {
          if (response.status !== 200) {
            throw new Error('error!');
          }

          return response.data;
        })
        .then(function(data) {
          window.location = '/';
        })
        .catch(function(err) {
          alert(err);
        })
    }
  }
});
</script>
```

3. 新建注册页面 views/signup.ejs 。

注册页面会比登录页面多一个重复密码的输入，这是为了保证用户在输入新密码时不出错。

```html
<div class="col-md-4 col-md-offset-4">
  <h1>登录</h1>
  <div class="form-group">
    <input type="text" class="form-control" v-model="name" placeholder="账号">
  </div>
  <div class="form-group">
    <input type="password" class="form-control" v-model="pass" placeholder="密码">
  </div>
  <div class="form-group">
    <input type="password" class="form-control" v-model="rePass" placeholder="密码">
  </div>
  <div class="form-group">
    <button class="btn btn-default" v-on:click="submit">提交</button>
  </div>
</div>
<script>

var vm = new Vue({
  el: '#app',
  data: {
    name: '',
    pass: '',
    rePass: ''
  },
  methods: {
    submit () {
      axios.post('/api/v1/signup',
        {
          name: vm.name,
          pass: vm.pass,
          rePass: vm.rePass
        })
        .then(function(response) {
          if (response.status !== 200) {
            throw new Error('error!');
          }

          return response.data;
        })
        .then(function(data) {
          window.location = '/';
        })
        .catch(function(err) {
          alert(err);
        })
    }
  }
});
</script>
```

#### 添加登录和注册的http请求处理

1. 在 route.api.js 中先引入 3个模块

```js
var bcrypt = require('bcrypt');
var UserModel = require('./models/user');
var config = require('./config');
```

一般存在数据库里的密码不能是明文的，bcrypt 这个库用来对密码做hash求值，将密码的hash值存在数据库中。

2. 处理 api/v1/signup 请求

```js
/* POST signup user */
router.post('/signup', function(req, res, next) {
  var name = req.body.name;
  var pass = req.body.pass;
  var rePass = req.body.rePass;

  if (pass !== rePass) {
    return errorHandle(new Error('两次密码不对'), next);
  }

  var user = new UserModel();
  user.name = name;
  user.pass = bcrypt.hashSync(pass, 10);
  user.save(function(err) {
    if (err) {
      errorHandle(err, next);
    } else {
      res.end();
    }
  });
});
```

处理注册请求时，要先对传入的两次密码进行对比，如果不一致就返回错误的响应。

在把注册信息保存到数据时，要将密码进行hash求值，数据库一定不能存密码明文。这不仅为了安全考虑，也是对用户的一个尊重。

3. 处理 api/v1/signin 请求

```js
/* POST signin user */
router.post('/signin', function(req, res, next) {
  var name = req.body.name || '';
  var pass = req.body.pass || '';

  UserModel.findOne({ name }, function(err, user) {
    if (err || !user) {
      return errorHandle(new Error('找不到用户'), next);
    } else {
      var isOk = bcrypt.compareSync(pass, user.pass);
      if (!isOk) {
        return errorHandle(new Error('密码不对'), next);
      }

      var authToken = user._id;
      var opts = {
        path: '/',
        maxAge: 1000 * 60 * 60 * 24 * 30, // cookie 有效期30天
        signed: true,
        httpOnly: true
      };

      res.cookie(config.cookieName, authToken, opts);
      res.end();
    }
  });
});

module.exports = router;
```

验证登录信息，要把用户输入的账号和密码与数据库里的账号和密码进行比较，如果一致，表明是一个已经注册过的用户。

上面把 user._id 当做 token 存在了 res.cookie 里。这里的核心是 res.cookie() 这个函数，它可以往 reponse 响应体内存入 cookie 信息以便浏览器能获得里面的信息。

opts这个对象里记录了一些cookie的配置信息，这里最重要的是 maxAge这个属性，它告诉浏览器，这个cookie只能有效多长时间。


#### 判断每一个http请求的cookie信息

每一次http请求，服务都需要判断cookie中的信息，以确保当前发起请求的用户是否是登录状态。

1. 新建一个中间件文件 ./middlewares/auth.js

```js
var config = require('../config');
var UserModel = require('../models/user');

function authUser(req, res, next) {
  const authToken = req.signedCookies[config.cookieName] || '';
  res.locals.currentUser = null;

  if (authToken) {
    UserModel.findOne({ _id: authToken }, function(err, user) {
      if (err) {
        next();
      } else {
        res.locals.currentUser = user;
        next();
      }
    });
  } else {
    next();
  }
}

module.exports = { authUser };
```

authUser函数会把每一个请求的 cookie 数据读出来，因为服务指导cookie里存了什么，所以，在这里把读出来的值直接取数据库查用户信息即可。

查到的用户信息存在res.locals.currentUser中。为什么存在这里？为了在视图引擎在处理ejs时能读到登录的用户信息。

2. 在 app.js 中植入验证用户信息的中间件

```js
...

var auth = require('./middlewares/auth');

...

app.use(auth.authUser);

app.use('/', page);
app.use('/api/v1', api);
```

#### 根据用户信息弹性的构建导航条

前面我们获取到了登录用户的信息并存在了res.locals.currentUser中。

在构建 ejs 是，可以利用currentUser这个对象来弹性的构建导航条信息。

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
      <ul class="nav navbar-nav navbar-right">
        <% if(currentUser) {%>
        <li class="dropdown">
          <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false"><%= currentUser.name %> <span class="caret"></span></a>
          <ul class="dropdown-menu">
            <li><a href="/signout">退出</a></li>
          </ul>
        </li>
        <% } else { %>
          <li><a href="/signin">登录</a></li>
          <li><a href="/signup">注册</a></li>
        <% } %>
      </ul>
    </div>
  </div>
</nav>
```

## 实例

参考 [first-app-sample-17](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-17)
