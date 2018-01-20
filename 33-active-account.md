# 邮箱激活账号 

用邮箱激活账号，在注册的时候必须先让用户输入个人邮箱。先把准备工作做好。

在注册页面添加邮箱的输入

```html
// ./src/views/signup.html

  <h1>注册</h1>
  <div class="form-group">
    <input type="text" class="form-control" v-model="name" placeholder="账号">
  </div>

  <div class="form-group">
    <input type="email" class="form-control" v-model="email" placeholder="邮箱">
  </div>

  ...
```

相应的，在 Vue 的 data 以及 axios.post('/api/v1/signup')部分添加 email，以保证 email 能正常送到服务端。

```js
// ./src/views/signup.html

var vm = new Vue({
  el: '#app',
  data: {
    name: '',
    email: '',
    pass: '',
    rePass: ''
  },
  methods: {
    submit () {
      axios.post('/api/v1/signup',
        {
          name: vm.name,
          email: vm.email,
          pass: vm.pass,
          rePass: vm.rePass
        })
        .then(function(response) {
          return response.data;
        })
        .then(function(data) {
          alert(data.message);
        })
        .catch(function(err) {
          alert(err.response.data.error);
        })
    }
  }
});
</script>
```

在服务端处理注册逻辑时，也要增加对 email 新字段的处理，这一块可以参考代码

```
./src/controllers/user.js
./src/models/user.js
```

当注册时，增加了 email 后，我们可以在 user 表中增加一个字段 active 来标识该邮箱是否被激活。

```js
// ./src/models/user.js

import mongoose from 'mongoose';
const Schema = mongoose.Schema;

const UserSchema = new Schema({
  name: String,
  email: String,
  pass: String,
  active: Boolean
});

const UserModel = mongoose.model('User', UserSchema);

export default UserModel;
```

激活的处理原理并不是很难。
1. 在注册时，我们可以把 email 和 pass 拼接在一起后生成一个 md5 字符串，这个 md5 字符串可以称为激活用的 token。
2. 用 key 拼一个 url。比如：localhost:3000/activeAccount?key=token&name=name
3. 给用户发一封邮件，邮件内容是一个包含<a href="ocalhost:3000/activeAccount?key=token&name=name">激活</a>

如果用户在注册的时候填错了邮箱，这个激活的 url 也就发给了别人。所以，在注册时要提示用户填写自己的邮箱。

完成以上需要安装3个第三方库。

```
$ npm install --save nodemailer
$ npm install --save nodemailer-smtp-transport
$ npm install --save utility // md5 库
```

在注册成功后，主动发送激活邮件。

```js
// ./src/controllers/user.js

import { sendActiveMail } from '../common/mail';
import utility from 'utility';

export const signup = function (req, res, next) {
  const { name, email, pass, rePass } = req.body;

  if (pass !== rePass) {
    return next(new Error('两次密码不对'));
  }

  const user = new UserModel();
  user.name = name;
  user.email = email;
  user.pass = bcrypt.hashSync(pass, 10);
  user.save(function (err) {
    if (err) {
      next(err);
    } else {
      sendActiveMail(
        email,
        utility.md5(user.email + user.pass),
        name
      );

      res.json({
        message: `欢迎加入${
          config.name
          }！我们已给您的注册邮箱发送了一封邮件, 请点击里面的链接来激活您的帐号。`
      });
    }
  });
};
```

设计一个简单的发送邮件函数。如果你对发送邮件的处理不是很明白，可以先单独试验一下 [例子](http://code.7xinsheng.com/post/59eee25998dae164a86e8b8f)

```js
// ./src/common/mail.js

import mailer from 'nodemailer';
import smtpTransport from 'nodemailer-smtp-transport';
import config from '../config';

const transporter = mailer.createTransport(smtpTransport(config.email));
const SITE_ROOT_URL = `${config.url}`;

function sendMail(data) {
  const from = '<' + config.email.auth.user + '>';
  data.from = from;
  transporter.sendMail(data, err => {
    if (err) {
      console.log(err);
    }
  });
}

export function sendActiveMail(who, token, name) {
  const to = who;
  const subject = `${config.name}社区帐号激活`;
  const html =
    `<p>您好：${name}</p>` +
    `<p>我们收到您在${config.name}社区的注册信息，请点击下面的链接来激活帐户：</p>` +
    `<a href  = "${SITE_ROOT_URL}/api/v1/activeAccount?key=${token}&name=${name}">激活链接</a>` +
    `<p>若您没有在${config.name}社区填写过注册信息，说明有人滥用了您的电子邮箱，请删除此邮件，我们对给您造成的打扰感到抱歉。</p>` +
    `<p>${config.name} 谨上。</p>`;
  
  sendMail({
    to,
    subject,
    html
  });
}

```

在发送邮件时会用到发送邮箱以及一些邮箱配置信息，还有一些为了丰富激活邮件的配置信息，这些信息都放在 config.js 中。

```js
./src/config.js

export default {
  cookieName: 'your_cookie_name',
  jwtSecret: 'your_jwt_secret',
  mongodbUrl: 'mongodb://localhost:27017/firstapp',
  admin: 'admin',
  sessionSecret: 'your_session_secret',
  email: {
    host: 'smtp.qq.com',
    port: 465,
    auth: {
      user: 'xx@qq.com',
      pass: 'your pass',
    }
  },
  name: '我的社区',
  url: 'localhost:3000'
}
```

用户会触发了邮件中的 <a href="localhost:3000/activeAccount?key=token&name=name"> 激活</a>，所以，服务器端要对路由 /activeAccount 增加激活处理。

激活处理的主要逻辑是将对用用户在数据库中的 email和密码再次 md5一下，和路由发过来的 token 进行比较，如果一致，说明是本人触发的激活。

最后只要将 active 更新为 true 即可。

添加路由

```js
// ./src/route.api.js

/* GET active account */
router.get('/activeAccount', user.activeAccount);
```

激活处理

```js
export const activeAccount = function (req, res, next) {
  const { key, name } = req.query;

  UserModel.findOne({ name }, function (err, user) {
    if (err || !user) {
      return next(new Error('找不到用户'));
    } else {

      const key2 = utility.md5(user.email + user.pass);
      if (key !== key2) {
        return next(new Error('激活失败'));
      }

      user.active = true;
      user.save();

      const token = jwt.encode(
        {
          _id: user._id,
          name: user.name,
          isAdmin: user.name === config.admin,
          active: user.active,
          exp: moment()
            .add('days', 30)
            .valueOf()
        },
        config.jwtSecret
      );

      const opts = {
        path: '/',
        maxAge: moment()
          .add('days', 30)
          .valueOf(),
        signed: true,
        httpOnly: true
      };

      res.cookie(config.cookieName, token, opts);
      res.send('active successed!');
    }
  });
};
```

在更新完 active = true 后，再一次更新了 jwt 信息，目的是为了客户端能拿到最新的用户信息。

## 实例

参考 [first-app-sample-25](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-25)
