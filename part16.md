# 处理markdown

在nodejs中处理markdown格式非常简单，只需要一个第三方库即可。比如可以使用marked，也可以使用markdown-it。

在处理markdown之前要先把markdown的原理搞清楚，在书写markdown文档时很像在写html，它们属于标记语言，但markdown的书写更简单。

```
# title
## subtitle
![](https://ws3.sinaimg.cn/large/006tNc79ly1fj9zvfv9i7j30c80ap0t3.jpg)
```

markdown最终能显示出一定的样式主要还是通过某种手段转成了html。所以，想在代码中处理markdown内容，就必须使用第三方库来把内容转化成html格式后再显示。

下面就以第三方库marked为例说明。

安装marked

```
npm install marked --save
```

利用marked将md内容转化成html内容

```
var marked = require('marked');
var mdContent = '# title';
var htmlContent = marked(mdContent);
console.log(htmlContent);

// output: <h1>title</h1>
```

以上是官方给出的例子。

在first-app项目中

引入marked模块

```
var marked = require('marked');
```

在res.render页面之前，先把post.content内容转化成html

```
router.get('/posts/show', function (req, res, next) {
  PostModel.findOne({ _id: req.query.id }, function (err, post) {
    post.content = marked(post.content);
    res.render('show', {post});
  });
});
```

post的content内容已经被转换成html了，在show.ejs中要使用&lt;%- %&gt;，而不是&lt;%= %&gt;。&lt;%- %&gt;会内容当初html来显示。

```
<div>
  <h1><%= post.title %></h1>
  <div><%- post.content %></div>
</div>
```



