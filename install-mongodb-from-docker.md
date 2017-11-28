## Docker


Docker是一个应用容器引擎，开发者可以打包他们的应用及依赖包到一个可移植的容器中，也可以实现虚拟化，容器使用沙箱机制，因此不会污染电脑环境。

https://baike.baidu.com/item/Docker/13344470?fr=aladdin

更详细的定义参见[docker官网](https://www.docker.com/#/production)

你可能需要安装两个东西。

#### 安装 [Docker for mac] or [Docker for windows]

https://www.docker.com/docker-mac

上面是官网的下载链接，如果下载不了请参考[百度云盘](http://pan.baidu.com/s/1nvuu6qX)

道云有镜像可以让你快速下载，我把下载链接贴在这里。

道云在后面的设置加速器时会提到。你可以先根据需要安装。

| Mac用户                                    | Windows用户                                | 
| ---------------------------------------- | ---------------------------------------- | 
| [下载Docker for Mac](https://get.daocloud.io/docker-install/mac) | [下载Docker for Windows](https://get.daocloud.io/docker-install/windows) 



#### 安装Kitematic

安装Docker后并启动，在Docker的菜单里能看到“kitematic”选项，点击它会提示你下载kitematic。

 ![](/assets/kitematic.png)

如果下载不了请参考[百度云盘](http://pan.baidu.com/s/1nvuu6qX)

#### 加速器

都安装完后，下载镜像可能很慢。这时你需要一个加速器。

https://www.daocloud.io/mirror#accelerator-doc

参考链接的配置方式后，下载镜像会飞起来。

1. 要加速，先登录。

如果你没有登录，那么上面的链接，你是看不到链接的配置方式的，

其实你打开的那个页面，让你登录的那个提示的位置，就是放置你的加速镜像地址以及配置方法的。

因此你首先需要先注册一个账号。

2. 登录之后

重新打开<https://www.daocloud.io/mirror#accelerator-doc#accelerator-doc>翻到刚才的位置，就看到如何加速器地址和设置方法。

每个用户都有自己专用的加速地址，以下例子的地址可能对你并不可用。因此请你使用自己的账号对应的加速器地址，mac用户和Windows用户，请优先使用docker for Mac 和 docker for Windows 配置，docker-toolbox加速器设置是你最后的选择.docker-toolbox配置请看[看这里](http://guide.daocloud.io/dcs/daocloud-9153151.html#docker-toolbox)，你可以优先看下面的配置方法。

#### MacOS用户加速器设置

右键点击桌面顶栏的 docker 图标，选择 Preferences ，在 Daemon或者Advanced 标签 下的 **Registry mirrors** 列表中加入下面的镜像地址:

```
http://49e8d00f.m.daocloud.io
```

点击 Apply & Restart 或者右下角的restart按钮使设置生效。

#### Windows用户加速器设置

在桌面右下角状态栏中右键 docker 图标，修改在 Docker Daemon 标签页中的 json ，把下面的地址:

```
http://49e8d00f.m.daocloud.io
```

加到"registry-mirrors"的数组里。点击 Apply 。



#### Linux用户加速器设置

在终端里执行下面的命令

```
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://49e8d00f.m.daocloud.io
```

该脚本可以将 --registry-mirror 加入到你的 Docker 配置文件 /etc/docker/daemon.json 中。



## 安装mongodb

在Kitematic界面搜索mongo

 ![](/assets/install-mongodb-4.png)

 点击“create”按钮就可以下载并安装mongodb的镜像，完成后会自动启动mongodb。


 ![](/assets/install-mongodb-5.png)

 右边出现的端口就是你在程序中要用的，每个人端口可能不一样。

