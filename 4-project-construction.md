## 项目结构
总览
```
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

```
./bin
./bin/www
```

目录`./bin`下只有一个文件`www`,文件`www`是整个first-app的入口文件（也可称为first-app的启动文件），运行first-app时要指定运行该文件，然后由该文件的内部代码去调用./app.js。

#### /public

```
./public
./public/javascripts
./public/images
./public/stylesheets
./public/stylesheets/style.css
```

目录`/public`是WebAPP存放静态资源的地方。这些静态资源包含css文件、浏览器运行的js文件、图片文件等。静态资源不需要设置任何路由就能直接通过链接访问。

比如通过\`[http://localhost:3000/stylesheets/style.css\`](http://localhost:3000/stylesheets/style.css) 可以在浏览器直接读取style.css，这样做的目的是方便浏览器在解析html页面时更方便地获得静态资源。

#### /routes

```
./routes
./routes/index.js
./routes/users.js
```

目录`./routes`是处理所有路由逻辑的地方。

后面会详细讲解什么是路由。

#### /views

```
./views
./views/index.ejs
./views/layout.ejs
./views/error.ejs
```

目录`./views`是放置页面模板的地方。WebAPP要给用户返回html页面时，都是用`./views`中页面模板来生成html文件。

#### 配置文件

```
./package.json
```

文件`./package.json`是整个WebApp的管理依赖包的地方。

> 依赖包：当WebApp需要调用别人已经开发好的模块时，就需要将模块名和版本号写到到package.json里。这个模块就是WebApp的依赖包，或称为依赖模块。

除了管理依赖包，package.json还有WebApp的名字和版本号信息。

另外，还有一些脚本信息。比如，上一节提到的运行项目的指令。

```
DEBUG=first-app:* npm start
```

npm start中的start就是在package.json中定义的一个脚本。

执行`npm start`，事实上是在执行`node ./bin/www`。

每一个项目都是不同开发同学开发的，项目的入口文件可能都不同，如果大家都遵循一种原则，在package.json中配置好start的执行脚本，那么别人就知道直接执行npm start就可以运行项目了。

如果有必要，还可以在"scripts"结构里扩展更多的脚本。

#### 主程序文件

```
./app.js
```

前面提过`/bin/www`文件是整个WebApp的启动文件，在`/bin/www`文件中最核心的代码是调用`./app.js`，而整个WebApp最核心的代码就是`./app.js`。

