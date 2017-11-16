# markdown

在nodejs中处理markdown格式非常简单，只需要一个第三方库即可。比如可以使用 [marked](https://github.com/chjj/marked) ，也可以使用 [markdown-it](https://github.com/markdown-it/markdown-it)。

在处理 markdown 之前要先把 markdown 的简单原理搞清楚。书写 markdown 文档很像在写 html，它们属于标记语言，但 markdown 的书写更简单。

```
# title
## subtitle
![](https://ws3.sinaimg.cn/large/006tNc79ly1fj9zvfv9i7j30c80ap0t3.jpg)
```

markdown格式最终会通过翻译器翻译成html。[marked](https://github.com/chjj/marked) 或 [markdown-it](https://github.com/markdown-it/markdown-it) 就是第三方提供的翻译库。

```
# title

被翻译成html

<h1>title<h1>

## subtitle
被翻译成

<h2>subtitle<h2>
```

详细的markdown语法[请参考](http://www.appinn.com/markdown/basic.html)

下面就以第三方库marked为例说明。

#### 安装marked

```
npm install marked --save
```

#### 利用marked将md内容转化成html内容

```
var marked = require('marked');
var mdContent = '# title';
var htmlContent = marked(mdContent);
console.log(htmlContent);

// output: <h1>title</h1>
```

以上是官方给出的例子。

#### 在first-app项目中引入marked模块

```js
var marked = require('marked');
```

在res.render页面之前，先把post.content内容转化成html

```js
router.get('/posts/show', function (req, res, next) {
  var id = req.query.id;

  PostModel.findOne({ _id: id }, function (err, post) {
    post.content = marked(post.content);
    res.render('show', {post});
  });
});
```

post的content内容已经被转换成html了，在show.ejs中要使用&lt;%- %&gt;，而不是&lt;%= %&gt;。&lt;%- %&gt;会把内容当成html来显示。

```html
<div>
  <h1><%= post.title %></h1>
  <div><%- post.content %></div>
</div>
```
## 实例

参考 [first-app-sample-10](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-10)
