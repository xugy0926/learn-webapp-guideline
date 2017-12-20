# 构建 babel 编译结构

到目前为止，我们的项目并没有用到 es5 和 es6 的语法特性。为了使用最新的语法特性，我们必须用 babel 来编译项目。

#### 调整 package.json

babel 是在运行之前对整个项目进行编译，所以需要在开发依赖中添加一系列模块以保证编译的正常。

```js
"devDependencies": {
  "babel-core": "^6.26.0",
  "babel-runtime": "^6.26.0",
  "babel-preset-env": "^1.6.0",
  "babel-register": "^6.24.1",
  "dotenv": "^4.0.0",
  "nodemon": "^1.11.0",
  "rimraf": "^2.6.1"
}
```

在依赖中需要添加 babel-cli 模块

```js
"dependencies": {
  "babel-cli": "^6.24.1",
}
```

在项目的根目录下添加 .babelrc 文件

```js
{
  "presets": ["env"]
}
```

到目前为止配置工作已经完成，babel 在编译项目时会生产很多新的代码文件，为了区分编译前和编译后的代码，我们把以往的代码全部都放在 src 文件夹里。

将文件结构调整如下

```
first-app
  - src
      - bin
      - middlewares
      - models
      - public
      - views
      - app.js
      - config.js
      - route.api.js
      - route.page.js
  - .babelrc
  - package.json
```

最后，我们修改 packeage.json 里的 scripts，目的是用 babel 来编译项目，而不是只用 node 来执行项目。

```
"scripts": {
  "dev": "nodemon ./src/bin/www --exec \"node -r dotenv/config -r babel-register\"",
  "clean": "rimraf dist",
  "build": "npm run clean && mkdir -p dist && babel src -s -D -d dist"
}
```

如果是在开发状态下，只要执行 `npm run dev` 即可。

如果想编译整个项目，执行 `npm run build`，这条指令会在项目的根目录生产 dist 文件夹，这个文件夹里就是编译出来的代码，如果要启动编译后的代码，执行`node ./dist/bin/www`


## 实例

参考 [first-app-sample-21](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-21)

