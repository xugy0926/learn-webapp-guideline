# 准备

本指导书将以 Node 为开发环境讲解如何开发 WebApp，在学习之前需要安装 NVM 和 Node 运行环境。

#### 安装 NVM

[https://code.7xinsheng.com/post/59d3b2b9fbbefc4e650f4c14](https://code.7xinsheng.com/post/59d3b2b9fbbefc4e650f4c14)

安装 NVM 后，在终端执行下面命令查看 NVM 的版本，以判断是否安装成功。

```
$ nvm --version
```

#### 通过 NVM 安装 Node

```
$ nvm install 8.1.1
```

判断是否安装成功

```
$ node --version

// output: v8.1.1
```

安装完一个版本后，还可以继续安装其他 Node 版本

```
$ nvm install 6.4.0
```

可以列出目前装的所有 Node 版本

```
$ nvm list
```

可以随意在已经安装的 Node 版本之间切换

```
$ nvm use 6.4.0
```

关于 NVM 的更多使用请参考[这里](https://github.com/creationix/nvm#usage)。



