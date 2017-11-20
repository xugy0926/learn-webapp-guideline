# 更好的API能力

开发一个WebApp项目，项目能力全部体现路由上，路由可以称为WebApp提供给外界的API。

## API

路由|功能
---|---
[GET] /api/users | 创建项目时自带的功能（暂时留着）
[GET] /api/posts | 获取文章列表
[POST] /api/posts/create | 提交新文章
[POST] /api/posts/edit | 提交文章修改内容
[GET] /api/posts/one | 获取特定文章的内容

## 路由分类

前面我们将获取页面的路由和获取数据的路由进行了分类，这是一个极好的习惯。这其实是业务上的分类，不仅是对功能切分，在分工上可以有不同的工程师来分别维护两者。

#### API版本控制

有些时候我们还可以对api进行版本分类，以更好的升级api。

```
[GET] /api/v1/users
[GET] /api/v1/posts
[POST] /api/v1/posts/create
[POST] /api/v1/posts/edit
[GET] /api/v1/posts/one
```

在未来，你可以针对某个路由进行升级。比如下面单独升级/api/v1/posts/one 为 v2。

```
[GET] /api/v1/users
[GET] /api/v1/posts
[POST] /api/v1/posts/create
[POST] /api/v1/posts/edit
[GET] /api/v2/posts/one
```
#### 尽可能用http自身的方法来区分路由功能

http提供了很多方法，和数据有关的有以下几类。

```
GET（SELECT）：从服务器取出资源（一项或多项）。
POST（CREATE）：在服务器新建一个资源。
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
DELETE（DELETE）：从服务器删除资源。
```

依据这个原则，我们把API调整如下。

```
[GET] /api/v1/users
[GET] /api/v1/posts
[POST] /api/v1/posts/create
[PATCH] /api/v1/posts/edit
[GET] /api/v1/posts/one
```

#### 避免使用动词来区分路由

在上面用了create，edit这样的字眼，路由尽量避免。

```
[GET] /api/v1/users
[GET] /api/v1/posts  
[POST] /api/v1/posts // 去掉动词
[PATCH] /api/v1/posts  // 去掉动词
[GET] /api/v1/posts/one
```

对posts这个资源，通过http的GET，POST，PUT三个方法就能区分是做什么事情，不需要多余的动词加以区分。

#### 用资源ID而不是单词区分资源

```
[GET] /api/v1/posts  // 获取文章列表
[GET] /api/v1/posts/one // 获取某一个文章
```

改成

```
[GET] /api/v1/posts  // 获取文章列表
[GET] /api/v1/posts/:id // 获取某一个文章
```

##### 最终的路由

```
[GET] /api/v1/users
[GET] /api/v1/posts  
[POST] /api/v1/posts
[PATCH] /api/v1/posts/:id
[GET] /api/v1/posts/:id
```

#### 总结

1. 尽量用http的方法来区分功能。
2. 避免在路由上用动词区分功能。
3. 针对某个资源，用和该资源有关的id（或唯一值）

以上我们整理和数据有关的API，数据API属于服务端范畴，页面只能用唯一的HTTP的GET方法，而且页面是属于客户端范畴。因此，页面的路由设计相对宽容一些。在教程中并不做专门的讲解。

你可以参考这篇 restfull api设计指南

http://www.ruanyifeng.com/blog/2014/05/restful_api.html

最后，别忘了修改 app.js:

```
- app.use('/api', api);

+ app.use('/api/v1', api);
```

## 实例

参考 [first-app-sample-14](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-14)