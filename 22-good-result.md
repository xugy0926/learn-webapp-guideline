# 更好的结果处理

在 route.api.js 中，我们利用 res.json() 方法来响应请求并返回结果数据。

```
res.json({ success: false });
res.json({ success: true });
res.json({ success: true, postsList: posts });
```

在处理结果时，我们将错误分为两类。

1. WebAPP 不提供的能力。
2. WebAPP 自身的逻辑错误（或数据库发生的错误）。

#### WebAPP 不提供的能力

WebAPP 提供的页面和数据能力是有限的。当客户端发起了一个不存在的路由访问，势必拿不到页面或者数据。

在前面的小节中，我们知道在 app.js 中有统一的错误处理。

```js
// filepath: app.js

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});
```

#### 修改数据返回的错误

通过 {success: false} 或者 { success: true} 来区分是否存在错误逻辑似乎不是特别的好。

在 HTTP 请求是，响应中都会默认有一个 status 参数，成为状态码，如果状态码不为 2xx，就表明错误处理错误。为什么不用 res.status 返回500的方式呢？

我们可以借助这个统一的错误中枢来统一处理所有的错误。

```js
// filepath: route.api.js

/* GET posts lists */
router.get('/posts', function(req, res, next) {
  PostModel.find({}, {}, function(err, posts) {
    if (err) {
      res.json({ success: false });
    } else {
      res.json({ success: true, postsList: posts });
    }
  });
});
```

修改成

```js
// filepath: route.api.js

/* GET posts lists */
router.get('/posts', function(req, res, next) {
  PostModel.find({}, {}, function(err, posts) {
    if (err) {
      err.status = 500;
      next(err);
    } else {
      res.json({ postsList: posts });
    }
  });
});
```
我们在err对象中加入 status = 500，并把错误送给错误中枢统一返回给用户。

其实在 app.js 中的错误处理，如果你不主动设置 `err.status = 500`, 它会主动帮默认设置为 500。

```js
// filepath: app.js

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  // 如果不设置err.status，或默认设置为500
  res.status(err.status || 500);
  res.render('error');
});
```

因此，可以省略状态码的设置，直接调用 next(err)。

```js
// filepath: route.api.js

/* GET posts lists */
router.get('/posts', function(req, res, next) {
  PostModel.find({}, {}, function(err, posts) {
    if (err) {
      next(err);
    } else {
      res.json({ postsList: posts });
    }
  });
});
```

其他路由修改参考上面的样子。

#### 修改客户端（接受结果）的处理

在此前，posts.ejs 中获取数据是如下处理

```js
fetchData () {
  axios.get('/api/v1/posts')
    .then(function(response) {
      vm.postsList = response.data.postsList;
      vm.postsList.forEach((element) => element.url = '/posts/show?id=' + element._id);
    })
}
```

这个处理并没有进行错误检查。上面我们知道，当服务端出现错误会返回状态码500。因此我们要接住这个错误才能保证逻辑正确处理。

```js
fetchData () {
  axios.get('/api/v1/posts')
    .then(function(response) {
      return response.data;
    })
    .then(function(data) {
      vm.postsList = data.postsList;
      vm.postsList.forEach((element) => element.url = '/posts/show?id=' + element._id);
    })
    .catch(function(err) {
      alert(err);
    })
}
```
axios.get() 发起一个 get 请求，如果返回的状态码不是 2xx，就会抛出一个异常并执行 catch() 函数。

以下是 [axios 文档](https://github.com/axios/axios#handling-errors)给出的说明。

> The request was made and the server responded with a status code that falls out of the range of 2xx

在views文件夹其他页面中获取数据的处理请参考上面的案例修改。

## 总结

1. 尽量通过统一的错误处理逻辑。
2. 尽量使用状态码，而不是自己设计一个错误值。例如：success：false/true。
3. 客户端页面要对错误进行错误处理以保证健壮性。

## 创建和修改数据时，该不该返回数据？（特别提醒）

在 route.api.js中创建post的路由处理中，可以把写入到数据库的数据重新返回给客户端。

```js
/* POST create post */
router.post('/posts', function(req, res, next) {
  var title = req.body.title;
  var content = req.body.content;

  var post = new PostModel();
  post.title = title;
  post.content = content;
  post.save(function(err, doc) {
    if (err) {
      next(err);
    } else {
      res.json({post: doc}); // 注意这里
    }
  });
});
```

一般情况下，新创建的数据需要返回给客户端一份，为什么呢？因为写入到数据库后会给这条数据产生一个唯一的id，而客户端拿到这个id可以进行一些交互操作。

比如，在 .views/create.js中

```js
submit () {
  axios.post('/api/v1/posts',
    {
      title: vm.title,
      content: vm.content
    })
    .then(function(response) {
      return response.data;
    })
    .then(function(data) {
      // 注意这句
      window.location = '/posts/show?id=' + data.post._id;
    })
    .catch(function(err) {
      alert(err);
    })
  }
}
```

可以通过服务端返回的post数据来打开刚刚创建的文章页面。

编辑文章时，服务端不需要返回完整的数据。

```js
/* PATCH edit post */
router.patch('/posts/:id', function(req, res, next) {
  var id = req.params.id;
  var title = req.body.title;
  var content = req.body.content;

  PostModel.findOneAndUpdate({ _id: id }, { title, content }, function(err) {
    if (err) {
      next(err);
    } else {
      res.json({}); // 不需要返回文章数据
    }
  });
});
```

因为编辑是对一个已经在数据库中的数据进行编辑，客户端自身有这条数据的id。
所以，在 ./views/edit.ejs中，主需要用本地已经有的id来进行页面跳转。

```js
submit () {
  axios.patch('/api/v1/posts/' + postId,
    {
      title: vm.title,
      content: vm.content
    })
    .then(function(response) {
      return response.data;
    })
    .then(function(data) {
      // 注意这里，用的是既有的postId来跳转到文章页面
      window.location = '/posts/show?id=' + postId
    })
    .catch(function(err) {
      alert(err);
    })
}

```

这也说明了http的post和patch的使用区别，post可能需要服务端返回数据，patch不需要服务端返回数据。

## 实例

参考 [first-app-sample-15](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-15)
