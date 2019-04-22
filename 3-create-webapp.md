# 基于 Express 创建 WebAPP

#### 安装 Express

```bash
$ npm install -g express-generator@4
```

-g 是 global 的意思。express-generator@4 会被安装到全局位置。当你打开电脑终端后，可以在任意一个目录下执行 express 命令。

express-generator@4 中的 4 是版本代号，表示安装版本为 4.x。目前，Express 的 4.x 是最新的版本。

#### 创建一个新的 WebAPP

- 进入你任何一个平时工作或学习用的文件夹

```bash
$ cd your_work_directory
```

- 创建 first-app 文件夹

在当前目录下创建 first-app 文件夹。

```bash
$ mkdir first-app
```

目录 first-app 就是你的工程（项目）文件夹。

- 进入 first-app 目录

```bash
$ cd first-app
```

- 在当前目录执行 express 来创建一个基于 Express 的 WebApp

```bash
$ express --view=ejs
```

执行 express 后，要注意观察输出的信息，特别是 install dependencies 和 run the app 的信息，后面会用到。

#### 安装 first-app 的依赖包

安装所有 package.json 中的依赖包。

```bash
$ npm install
```

#### 运行 first-app

mac 系统

```bash
$ DEBUG=first-app:* npm start
```

windows 系统

```bash
$ set DEBUG=first-app:* npm start
```

不管是 mac 系统还是 windows 系统，也可以直接通过 npm start 启动。

```bash
$ npm start
```

甚至也可以不用 npm start，直接通过 node 命令来启动项目。

```bash
$ node ./bin/www
```

#### 预览结果

浏览器打开 localhost:3000
