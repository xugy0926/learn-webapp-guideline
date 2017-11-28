## 基于Express创建WebAPP

#### 安装express

```
$ npm install -g express-generator@4
```

-g 是global的意思。表示npm安装express-generator@4时会安装到全局位置。当你打开终端后，可以在任意一个目录下执行express。

express-generator@4中的4是版本代号，表示安装版本为4的express。express是从版本1一直演进到版本4。选择版本4是最新版本。

#### 创建一个新的WebApp

* 进入你任何一个平时工作或学习用的文件夹

```
$ cd your_work_directory
```

* 创建first-app文件夹

```
$ mkdir first-app
```

目录first-app就是你的工程文件夹。

* 进入first-app目录

```
$ cd first-app
```

* 在当前目录执行express来创建一个基于Express的WebApp

```
$ express --view=ejs
```

执行express后，要注意观察输出的信息，特别是install dependencies和run the app的信息。后面会用到。

#### 安装first-app的依赖包

```
$ npm install
```

#### 运行first-app

```
$ DEBUG=first-app:* npm start
```

windows用户

```
$ set DEBUG=first-app:* npm start
```

#### 预览结果

浏览器打开 localhost:3000

