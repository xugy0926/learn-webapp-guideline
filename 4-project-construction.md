# 项目结构

总览

```bash
- ../my_project/first-app/          # 工程目录
    - bin/                          # 存放命令行相关代码
        www                         # express的入口文件
    - public/                       # 存放静态资源
        javascripts
        images
        + stylesheets
    - routes/                       # 存放路由逻辑
        index.js
        users.js
    - views/                        # 存放页面模板
        index.ejs
        layout.ejs
        error.ejs
    package.json                    # 配置文件
    app.js                          # 主程序文件

```

#### /bin

```bash
./bin
./bin/www
```

目录`./bin`下只有一个文件`www`,文件`www`是整个 first-app 的入口文件（也可称为 first-app 的启动文件），运行 first-app 时要指定运行该文件，然后由该文件的内部代码去调用./app.js。

#### /public

```bash
./public
./public/javascripts
./public/images
./public/stylesheets
./public/stylesheets/style.css
```

目录`/public`是 WebAPP 存放静态资源的地方。这些静态资源包含 css 文件、浏览器运行的 js 文件、图片文件等。静态资源不需要设置任何路由就能直接通过链接访问。

比如通过\`[http://localhost:3000/stylesheets/style.css\`](http://localhost:3000/stylesheets/style.css) 可以在浏览器直接读取 style.css，这样做的目的是方便浏览器在解析 html 页面时更方便地获得静态资源。

#### /routes

```bash
./routes
./routes/index.js
./routes/users.js
```

目录`./routes`是处理所有路由逻辑的地方。

后面会详细讲解什么是路由。

#### /views

```bash
./views
./views/index.ejs
./views/layout.ejs
./views/error.ejs
```

目录`./views`是放置页面模板的地方。WebAPP 要给用户返回 html 页面时，都是用`./views`中页面模板来生成 html 文件。

#### 配置文件

```bash
./package.json
```

文件`./package.json`是整个 WebApp 的管理依赖包的地方。

> 依赖包：当 WebApp 需要调用别人已经开发好的模块时，就需要将模块名和版本号写到到 package.json 里。这个模块就是 WebApp 的依赖包，或称为依赖模块。

除了管理依赖包，package.json 还有 WebApp 的名字和版本号信息。

另外，还有一些脚本信息。比如，上一节提到的运行项目的指令。

```bash
DEBUG=first-app:* npm start
```

npm start 中的 start 就是在 package.json 中定义的一个脚本。

执行`npm start`，事实上是在执行`node ./bin/www`。

每一个项目都是不同开发同学开发的，项目的入口文件可能都不同，如果大家都遵循一种原则，在 package.json 中配置好 start 的执行脚本，那么别人就知道直接执行 npm start 就可以运行项目了。

如果有必要，还可以在"scripts"结构里扩展更多的脚本。

#### 主程序文件

```bash
./app.js
```

前面提过`/bin/www`文件是整个 WebApp 的启动文件，在`/bin/www`文件中最核心的代码是调用`./app.js`，而整个 WebApp 最核心的代码就是`./app.js`。
