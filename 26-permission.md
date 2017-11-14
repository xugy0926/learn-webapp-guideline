# 访问权限

当有了账户功能后，可以用是否是登录状态来设置一些功能。

#### 给 post 添加作者 id

数据库中只存储 post 的 title 和 content两个字段，有了账户信息后，可以存储作者的 id。

// filepath: models/post.js
```js
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var ObjectId = Schema.ObjectId;

var PostSchema = new Schema({
    title: String,
    content: String,
    authorId: ObjectId, // 添加作者 ID。
});

const PostModel = mongoose.model('Post', PostSchema);

module.exports = PostModel;
```

在处理 post 请求时，将 res.locals.currentUser._id 的值存到 post的authorId，这样就会把作者的 id 写入到数据库中。

// filepath: route.api.js
```js
/* POST create post */
router.post('/posts', function (req, res, next) {
  var title = req.body.title;
  var content = req.body.content;
  
  var post = new PostModel();
  post.title = title;
  post.content = content;
  post.authorId = res.locals.currentUser._id;
  post.save(function(err, doc) {
    if (err) {
      errorHandle(err, next);
    } else {
      res.json({ post: doc });
    }
  });
});
```
#### 限制新建 post 的处理

在 views/posts.ejs 中有一个新建文章的菜单，这里应该限制登录的用户才能创建文章。

判断currentUser变量是否存在，如果存在说明用户是登录状态，才显示创建菜单

// filepath: views/posts.ejs
```html
<% if(currentUser) { %>
<!-- Split button -->
<div class="btn-group pull-right">
  <button type="button" class="btn btn-default">操作</button>
  <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
    <span class="caret"></span>
    <span class="sr-only">Toggle Dropdown</span>
  </button>
  <ul class="dropdown-menu">
    <li><a href="/posts/create">新建</a></li>
  </ul>
</div>
<% } %>
```

#### 显示编辑 post 的处理

一般情况下只能登录用户是文章作者才能编辑文章，所以要对编辑菜单进行判断。

判断 currentUser 存在并且 currentUser的 id值和 post 的作者一致才显示编辑惨淡。

// filepath: views/show.ejs
```html
  <% if(currentUser && currentUser._id.toString() === post.authorId.toString() ) { %>
  <!-- Split button -->
  <div class="btn-group pull-right">
    <button type="button" class="btn btn-default">操作</button>
    <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
      <span class="caret"></span>
      <span class="sr-only">Toggle Dropdown</span>
    </button>
    <ul class="dropdown-menu">
      <li><a href="/posts/edit?id=<%= post._id %>">编辑</a></li>
    </ul>
  </div>
  <% } %>
```

#### 整理配置信息（改善）

将链接mongodb的url写入到config.js中，这样可以更方便管理webapp的各种配置信息。

// filepath: config.js
```js
module.exports = {
  cookieName: 'your_cookie_name',
  mongodbUrl: 'mongodb://localhost:32768/firstapp'
}
```

链接数据时只需要使用 config 的 mongodbUrl 即可

// filepath: models/init.js
```js
var mongoose = require('mongoose');
var config = require('../config');

mongoose.connect(config.mongodbUrl, {
  useMongoClient: true
});
```
## 实例

参考 [first-app-sample-18](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-18)
