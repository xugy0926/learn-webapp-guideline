# 配置执行项目的脚本

当修改了 first-app 的代码后，总是要先停止项目然后再重新启动 first-app。

在终端输入快捷组合键`ctrl + c`停止运行 first-app 服务，再执行运行指令运行服务。

```bash
输入ctrl + c

$ DEBUG=first-app:* npm start
```

上面是正常的做法，但很麻烦。我们可以利用 supervisor 来监听文件被修改后自动重启项目。

#### 全局配置 supervisor

安装 supervisor 模块，一般采用全局安装，这样可以保证在终端的任何地方都可以直接使用 supervisor。

```bash
$ npm install -g supervisor
```

相比与全局安装，我更建议你在项目中安装 supervisor，两个原因。

1. 不同的项目可以有不同版本的 supervisor。当你下载了一个很老的项目，可能它依赖更早的 supervisor 版本，如果此时用了全局安装的 supervisor 会有问题。
2. 每个项目的依赖环境越独立，这个项目就更安全、执行更方便。

```bash
$ cd first-app
$ supervisor DEBUG=first-app:* node ./bin/www
```

如果你全局安装了 supervisor 可以卸载

```bash
$ npm uninstall --global supervisor
```

#### 在项目中配置 supervisor 脚本

在 first-app 项目中安装 supervisor.

先进入到 first-app 文件夹

```bash
$ cd first-app

$ npm install --save supervisor
```

通过 npm install 安装 supervisor 时带上--save 参数，会把 supervisor 配置到 package.json 中。这时可以去 package.json 中检查 dependencies 中是否有了如下属性。

```json
"supervisor": "^0.12.0"
```

然后在 package.json 的 script 字段中添加一个 auto-start 脚本

```json
"scripts": {
  "start": "node ./bin/www",
  "auto-start": "./node_modules/.bin/node-supervisor node ./bin/www"
},
```

以上执行成功后，往后只要执行下面指令就可以自动重启项目了

```bash
npm run auto-start
```

## 实例

参考 [first-app-sample-2](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-2)
