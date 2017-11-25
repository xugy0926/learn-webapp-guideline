# 持久化存储 - mongodb

要把数据持久化就要用到数据库，比如sqllit，mysql等比较有名的数据库，除此之外，mongodb。在开发小型项目时mongodb足够用了，nodejs必须引用node-mongodb-native这个库来操作mongodb数据库，庆幸的是还可以通过mongoose，mongoose是mongodb数据库的模型工具库，专门为node.js设计，mongoose基于node-mongodb-native并工作在异步环境下，或者你可以直接认为mongoose是node-mongodb-native的拓展库，相比操作node-mongodb-native，操作mongoose时可以简化不少代码。

mongoose官网[http://mongoosejs.com/](http://mongoosejs.com/)

以下内容只作为对操作mongodb和mongoose的认识，不需要按照本篇文档操作项目。

在学习操作数据库技术一般先从以下步骤进行。

1. 安装mongodb数据库。
2. 通过nodejs代码连接到数据库。
3. 构建模型 - 表。
4. 操作表 （增、删、改、查）

## 安装mongodb

在本地安装mongodb可自行google。

在本地安装数据库时都会污染电脑的环境，避免这种情况发生可以采用docker容器的方式来安装mongodb。但在在之前必须安装docker。

在docker中安装mongodb可以参考[附: 用Docker安装mongodb][1]

## 添加mongoose

在项目中添加mongoose模块
```
$ npm install --save mongoose
```

## 连接数据库

当本地运行mongodb数据后，nodejs代码要连上去非常容易。四行代码就能搞定。

```js
var mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/firstapp', {
  useMongoClient: true
});
```

* 以上就是通过引入第三方模块mongoose来连接mongodb数据库。

* 数据库的端口号根据需求进行调整，一般直接安装mongodb并启动后，端口号默认是27017，如果通过docker安装，端口号会是其他的。

* firstapp就是连接mongodb制定的数据库名，如果mongodb没有这个数据库就会新建一个。

## 定义模型 - 表

```js
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var PostSchema = new Schema({
    title: String,
    content: String
});

const PostModel = mongoose.model('Post', PostSchema);
```

* 通过mongoose的Schema可以构建一个表模型，在上面构建了PostSchema模型，模型有两个字段title和content。
* 将PostSchema模型关联到'Post'这张表中，如果没有Post这张表就创建一个。这种关联很有意思，假如你想在PostSchema中增加一个字段，只需要加上并重新启动应用即可，mongoose会自动帮你在数据库表中增加一个字段。

## 操作表

利用PostModel来操作表（增、删、查、改）

#### 增

```js
var post = new PostModel();

post.title = title;
post.content = content;

post.save(function (err, doc) {
  res.send(doc);
});
```

#### 删

```js
PostModel.remove({_id: postId}, function (err) {
  if (err) handleError(err);
  //removed! success!
});
```

依据一个postId条件来删除对应的post。

#### 查

```js
PostModel.find({}, function (err, docs) {
  res.json({ postsList: docs });
});
```

也可以只查制定的一个

```js
PostModel.findOne({_id: postId}, function (err, doc) {
  res.json({ postsList: docs });
});
```

#### 改

```js
PostModel.findOne({_id: postId}, function (err, doc) {
  if (err) handleError(err);
  doc.title = '修改标题';
  doc.content = '修改内容';
  doc.save();
});
```

```js
PostModel.update({_id: postId}, { title: '修改标题', content: '修改内容'}, function(err, doc) {
  if (err) handleError(err);
  // updated! success!
});
```

在操作数据库时有几点要注意。

1. 新增内容，只需要把内容填入即可。
2. 删、改、查这三个操作必须给定明确的条件，否则无法操作。


  [1]: https://github.com/xugy0926/learn-webapp-guideline/blob/master/install-mongodb-from-docker.md
