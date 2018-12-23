# ejs

#### 什么是 ejs

ejs 全称 Embedded JavaScript templating（嵌入式 JavaScript 模板）。

E 代表嵌入式（Embedded）。如果用 ”有效（Effective）“、“优雅（Elegant）”或“简单（Easy）”来表述也没有问题。

ejs 是一种简单的模板语言，可以编写 JavaScript 代码来生成 HTML 页面。 再次重申，ejs 真的没什么神奇，它只是一些 JavaScript 代码，提供了一些可以方便生成 HTML 页面的函数而已。为了更方便的把数据反映到页面上，它规定了一些特殊写法的模板语句。这可能是大多数人把 ejs 成为模板语言的原因。

下面就用最简单的例子来说明 ejs 的用法。



##### 创建测试项目文件夹并进入

```
$ mkdir test_ejs
$ cd test_ejs
```

##### 创建项目 package.json

```
$ npm init
```

一直按`回车键`结束。

##### 安装 ejs 库

```
$ npm install ejs
```

##### 创建 index.js 文件

```
$ touch index.js
```

##### 在 index.js 中输入下面四行行代码

```javascript
const ejs = require('ejs')

const people = ['geddy', 'neil', 'alex']
const template = '<%= people.join(", "); %>'
const content = ejs.render(template, {people: people})

console.log(content)
```

##### 运行

```
$ node index.js

// output: geddy, neil, alex
```

ejs 的 render 函数接收两个参数，第一个参数为模板，第二个参数为数据。

在模板里通过 `<%= %>`方式来执行数据变量。

ejs 只认识 `<%= %>`里的代码，其他都会当成字符串来处理（其实就是不做任何处理）。

给模板加描述前缀

```javascript
const template = '名单：<%= people.join(", "); %>'
const content = ejs.render(, {people: people})

// output: 名单：geddy, neil, alex
```

给模板加 html 标签

```javascript
const template = '<h1><%= people.join(", "); %></h1>'
const content = ejs.render(template, {people: people})

// output: <h1>geddy, neil, alex</h1>
```

如果把输出的内容保存在 html 文件中，就可以正常用浏览器访问了。

```javascript
const ejs = require('ejs')
const fs = require('fs')

const people = ['geddy', 'neil', 'alex']
const template = '<h1><%= people.join(", "); %></h1>'
const content = ejs.render(template, {people: people})

fs.writeFileSync('names.html', content)
```

然后就可以用浏览器打开 names.html 文件了。

构建一个 `<li>`列表。

```javascript
const ejs = require('ejs')
const fs = require('fs')

const people = ['geddy', 'neil', 'alex']
const template = `
  <% for(let i = 0; i < people.length; i++) { %>
    <li><%= people[i] %></li>
  <% } %>
`

const content = ejs.render(template, {people: people})

fs.writeFileSync('names.html', content)
```

你还可以丰富模板。

```javascript
const ejs = require('ejs')
const fs = require('fs')

const people = ['geddy', 'neil', 'alex']
const template = `
<html>
  <head>
    <link href="https://cdn.bootcss.com/twitter-bootstrap/4.1.3/css/bootstrap.min.css" rel="stylesheet">
  </head>
  <body>
    <div class="container">
      <% for(let i = 0; i < people.length; i++) { %>
        <li><%= people[i] %></li>
      <% } %>
    </div>
  </body>
</html>
`

const content = ejs.render(template, {people: people})

fs.writeFileSync('names.html', content)
```

你还可以把模板单独放在独立文件中。

创建模板文件 template.html

```html
<html>
  <head>
    <link href="https://cdn.bootcss.com/twitter-bootstrap/4.1.3/css/bootstrap.min.css" rel="stylesheet">
  </head>
  <body>
    <div class="container">
      <% for(let i = 0; i < people.length; i++) { %>
        <li><%= people[i] %></li>
      <% } %>
    </div>
  </body>
</html>
```

读取模板并渲染内容

```javascript
const ejs = require('ejs')
const fs = require('fs')

const people = ['geddy', 'neil', 'alex']
const template = fs.readFileSync('./template.html', 'utf-8')
const content = ejs.render(template, {people: people})

fs.writeFileSync('names.html', content)
```

在模板里，可以用任何合法的 JavaScript 语句。

下面将 for 循环换成 forEach。

```html
<html>
  <head>
    <link href="https://cdn.bootcss.com/twitter-bootstrap/4.1.3/css/bootstrap.min.css" rel="stylesheet">
  </head>
  <body>
    <div class="container">
      <% people.forEach(function(item) { %>
        <li><%= item %></li>
      <% }) %>
    </div>
  </body>
</html>
```

ejs 的模板中通过 `<%%>` 来构建可执行 JavaScript 的语句，就可以动态地构建出复杂的 html结构了。