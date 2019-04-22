# 准备

本书将以 Node 为开发环境讲解如何开发 WebApp，在学习之前需要安装 NVM 和 Node 运行环境。

#### 安装 NVM

[http://xugaoyang.com/post/59d3b2b9fbbefc4e650f4c14](http://xugaoyang.com/post/59d3b2b9fbbefc4e650f4c14)

安装 NVM 后，在终端执行下面命令查看 NVM 的版本，如果能显示版本号即代表 nvm 安装成功。

```
$ nvm --version

// output: 0.33.2
```

#### 通过 NVM 安装 Node

```
$ nvm install 8.12.0
```

查看 Node 版本，如果能显示版本号即代表 Node 安装成功。

```
$ node --version

// output: v8.12.0
```

安装完一个版本后，还可以继续安装其他 Node 版本。

```
$ nvm install 10.13.0
```

可以列出目前装的所有 Node 版本。

```
$ nvm ls
```

也可以随意在已经安装的 Node 版本之间切换。

```
$ nvm use 10.13.0
```

为了保证每次打开终端都能使用某个固定的 Node 版本。可以设置默认使用版本。

```
$ nvm alias default 8.12.0
```

关于 NVM 的更多使用请参考[这里](https://github.com/creationix/nvm#usage)。
