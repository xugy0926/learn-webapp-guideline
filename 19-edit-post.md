# 添加文章编辑页面

创建文章后，都会提供再编辑文章的功能。这个功能需要设计两个路由。一个用于访问编辑页面，另一个用于提交修改信息。

## 页面

路由|功能
---|---
[GET] /posts/edit | 编辑页面

## API

路由|功能
---|---
[POST] /api/posts/edit | 提交文章修改内容
[GET] /api/posts/one | 获取特定文章的内容

## 添加页面路由处理逻辑

#### 添加编辑按钮

在 views/show.ejs 中添加编辑按钮，用于触发打开编辑页面。

```html
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
```

#### 增加访问编辑页面逻辑

在 route.page.js 中添加对 /posts/edit的处理。

访问编辑页面的路由需要带一个query参数id。

```
posts/edit?id=xxxx
```

因此需要通过req.query对象取出id。取出的id直接给到edit页面。

```js
/* GET posts edit page. */
router.get('/posts/edit', function (req, res, next) {
  var id = req.query.id;

  res.render('edit', { id });
});
```

#### 添加获取某个特定id的文章

在 route.api.js 中添加 /posts/one的路由处理。

```js
/* GET one post */
router.get('/posts/one', function (req, res, next) {
  var id = req.query.id;

  PostModel.findOne({_id: id}, function(err, post) {
    if (err) {
      res.json({ success: false });
      return;
    }

    res.json({ success: true, post });
  });
});
```

#### 添加编辑更新某个特定id的文章

使用findOneAndUpdate更新数据，第二个参数 title和content表示要更新的字段。

```js
/* PATCH edit post */
router.post('/posts/edit', function(req, res, next) {
  var id = req.body.id;
  var title = req.body.title;
  var content = req.body.content;

  PostModel.findOneAndUpdate({ _id: id }, { title, content }, function(err) {
    if (err) {
      res.json({ success: false });
    } else {
      res.json({ success: true });
    }
  });
});
```


#### 添加编辑页面

新建 views/edit.ejs, 内容可以从views/create.ejs中拷贝过来。但在 `<script>` 中要做一些调整。

获取文章id，id 是 在render页面时传给页面的。

```js
var postId = '<%= id %>';
```

> 在`<script>`中要利用res.render传过来的变量，一样是使用<%= %>标签，在解释是会直接把id的值替换上去。

Vue初始化是在created()回调中直接抓取文章数据，并把内容给到 Vue的data中的title和content。

修改submit方法，提交并存储更改后的内容，记得把文章id传递过去。

```js
created () {
  axios.get('/api/posts/one?id=' + postId)
    .then(function(response) {
      vm.title = response.data.post.title;
      vm.content = response.data.post.content;
    })
    .catch(function(err) {
      alert(err);
    })
},
  methods: {
    submit () {
    axios.post('/api/posts/edit',
      {
        id: postId,
        title: vm.title,
        content: vm.content
      })
      .then(function(response) {
        alert(JSON.stringify(response.data));
      })
      .catch(function(err) {
        alert(err);
      })
    }
  }
```

这样就保证html加载时，回去根据id抓取文章内容。

## 实例

参考 [first-app-sample-12](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-12)
