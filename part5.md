# 配置执行项目的脚本

当修改了first-app的代码后，总是要先停止项目然后再重新启动first-app。

在终端输入快捷组合键`ctrl + c`停止运行first-app服务，再执行运行指令运行服务。

```
输入ctrl + c

$ DEBUG=first-app:* npm start
```

上面是标准做法，但很麻烦。我们可以利用supervisor来监听修改并自动重新项目。

#### 全局配置supervisor

安装supervisor模块。一般采用全局安装，这样可以保证在终端的任何地方都可以直接使用supervisor。

```
$ npm isntall -g supervisor
```

```
$ cd first-app
$ supervisor DEBUG=first-app:* node ./bin/www
```

#### 在项目中配置supervisor脚本

也可以把supervisor安装在first-app项目中。

```
npm install --save supervisor
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

以上执行成功后，往后只要执行下面指令就可以达到自动重启项目了

```
npm run auto-start
```

## 事例

参考事例 [first-app-sample-2](https://github.com/xugy0926/learn-webapp-sample/tree/master/first-app-sample-2)

