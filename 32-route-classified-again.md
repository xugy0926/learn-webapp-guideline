# 进一步分离路由和逻辑

前面虽然把 page 的路由和 api 的路由进行了分离，但是路由的处理和逻辑代码混杂在一起，结构上还是有点乱。

为了进一步分离路由和逻辑，我们把逻辑处理进行集中分类。

目前项目中有三大块逻辑处理。

- 页面逻辑
- 用户逻辑
- 文章逻辑

我们先在 src 文件夹创建一个 controllers 文件夹，在里面新建三个文件。

```
./src/controllers/user.js
./src/controllers/post.js
./src/controllers/page.js
```

剩下的事就是把对应的代码迁移到对象的逻辑模块即可。

这里举个例子。

```js
// filepath: ./src/route.api.js

/* POST create post */
router.post('/posts', auth.adminRequired, function(req, res, next) {
  const { title, content } = req.body;

  const post = new PostModel();
  post.title = title;
  post.content = content;
  post.authorId = res.locals.currentUser._id;
  post.save(function(err, doc) {
    if (err) {
      next(err);
    } else {
      res.json({ post: doc });
    }
  });
});
```

我们可以把逻辑放在 controllers/post.js 里

```js
// filepath: ./src/controllers/post.js

export const create = function(req, res, next) {
  const { title, content } = req.body;

  const post = new PostModel();
  post.title = title;
  post.content = content;
  post.authorId = res.locals.currentUser._id;
  post.save(function(err, doc) {
    if (err) {
      next(err);
    } else {
      res.json({ post: doc });
    }
  });
};
```

然后我们的路由这里就可以直接调用 create 这个函数


```js
// filepath: ./src/route.api.js

import * as post from './controllers/post';

/* POST create post */
router.post('/posts', auth.adminRequired, post.create);
```

如果迁移完所有的逻辑处理，路由的代码看起来就像这样。

```js
// filepath: ./src/route.api.js

import express from 'express';
import * as auth from './middlewares/auth';
import * as post from './controllers/post';
import * as user from './controllers/user';

const router = express.Router();

/* GET users lists. */
router.get('/users', user.more);

/* GET posts lists */
router.get('/posts', post.more);

/* GET one post */
router.get('/posts/:id', post.one);

/* POST create post */
router.post('/posts', auth.adminRequired, post.create);

/* PATCH edit post */
router.patch('/posts/:id', auth.adminRequired, post.update);

/* POST signup user */
router.post('/signup', user.singup);

/* POST signin user */
router.post('/signin', user.signin);

export default router;
```

这样，看起来就更清晰了。


## 实例

参考 [first-app-sample-24](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-24)
