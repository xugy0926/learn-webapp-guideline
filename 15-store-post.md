# 存储数据

先确保first-app项目已经引入了mongoose

```
$ cd first-app
$ npm install --save mongoose
```

first-app在启动项目时要连接到mongodb数据库。

为了更好的管理模块，在first-app下新建一个文件夹models/，该文件用于存放数据库相关的操作文件。

新建./models/init.js文件

```js
var mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:32770/firstapp', {
  useMongoClient: true
});
```

如果你安装mongoose的版本是>=5.x，要去掉 `{ useMongoClient: true }`，否则运行webapp时会报错:
> WARNING: The `useMongoClient` option is no longer necessary in mongoose 5.x, please remove it.

./models/init.js文件
```js
var mongoose = require('mongoose');

mongoose.connect('mongodb://192.168.99.100:32769/webapp2');
```

init.js就是数据库的初始化文件，可以在./app.js文件最开始引入init.js模板。

```js
// 在初始化app.js最开头就连接数据库
require('./models/init');
var express = require('express');
...
```

加载模块就会运行init.js，这样你引入的mongoose模块就连接上了数据库，后续只要操作mongoose即可。

新建Post模型，

新建./models/post.js文件

```js
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var PostSchema = new Schema({
    title: String,
    content: String
});

var PostModel = mongoose.model('Post', PostSchema);

module.exports = PostModel;
```

在route.api.js中引入./models/post.js模块

```js
var PostModel = require('./models/post');
```

将数据保存到数据库的post表中

```js
/* POST create post */
router.post('/posts/create', function(req, res, next) {
  var title = req.body.title;
  var content = req.body.content;

  var post = new PostModel();
  post.title = title;
  post.content = content;
  post.save(function (err, doc) {
    res.json({success: true});
  });
});
```

获取所有文章列表是从数据库查数据

```js
/* GET posts lists */
router.get('/posts', function(req, res, next) {
  PostModel.find({}, {}, function (err, posts) {
    if (err) {
      res.json({ success: false });
      return;
    }

    res.json({ success: true, postsList: posts });
  });
});
```

至此，从用户发送提交文章请求到保存到数据库的通路就完成了。而且api/posts路由会从数据库拉取文章列表并返回给用户。

因为postsList是从数据库拉取的对象数组，所以在./views/posts.ejs中要修改以下操作。

```html
<p v-for="item in postsList">{{ item }}</p>

改成

<p v-for="item in postsList">{{ item.title }}</p>
```

## 实例

参考 [first-app-sample-8](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-8)

