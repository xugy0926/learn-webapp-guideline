# 配置执行项目的脚本

当修改了first-app的代码后，总是要先停止项目然后再重新启动first-app。

在终端输入快捷组合键`ctrl + c`停止运行first-app服务，再执行运行指令运行服务。

```
输入ctrl + c

$ DEBUG=first-app:* npm start
```

上面是正常的做法，但很麻烦。我们可以利用supervisor来监听文件被修改后自动重起项目。

#### 全局配置supervisor

安装supervisor模块，一般采用全局安装，这样可以保证在终端的任何地方都可以直接使用supervisor。

```
$ npm install -g supervisor
```

```
$ cd first-app
$ supervisor DEBUG=first-app:* node ./bin/www
```

相比与全局安装，我更建议你在项目中安装supervisor，两个原因。
1. 不同的项目可以有不同版本的supervisor。当你下载了一个很老的项目，可能它依赖更早的superviaor版本，如果此时用了全局安装的supervisor会有问题。
2. 每个项目的依赖环境越独立，这个项目就更安全、执行更方便。

如果你全局安装了supervisor可以卸载
```
$ npm uninstall --global supervisor
```

#### 在项目中配置supervisor脚本

在first-app项目中安装supervisor.

先进入到first-app文件夹
```
$ cd first-app

$ npm install --save supervisor
```

通过npm install安装supervisor时带上--save参数，会把supervisor配置到package.json中。这时可以去package.json中检查dependencies中是否有了如下属性。

```
"supervisor": "^0.12.0"
```

然后在package.json的script字段中添加一个auto-start脚本

```
"scripts": {
  "start": "node ./bin/www",
  "auto-start": "./node_modules/.bin/node-supervisor node ./bin/www"
},
```

以上执行成功后，往后只要执行下面指令就可以自动重启项目了

```
npm run auto-start
```

## 事例

参考事例 [first-app-sample-2](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-2)

