## Kitematic 


Docker是一个应用容器引擎，开发者可以打包他们的应用及依赖包到一个可移植的容器中，也可以实现虚拟化，容器使用沙箱机制，因此不会污染电脑环境。

https://baike.baidu.com/item/Docker/13344470?fr=aladdin

更详细的定义参见[docker官网](https://www.docker.com/#/production)

你可能需要安装两个东西。

#### Docker for mac，or Docker for windows

https://www.docker.com/docker-mac

如果下载不了请参考[百度云盘](http://pan.baidu.com/s/1nvuu6qX)

#### 下载Kitematic

安装Docker后并启动，在Docker的菜单里能看到“kitematic”选项，点击它会提示你下载kitematic。

#### 安装

下载完成打开一路继续就安装好了。
 
 ![](/assets/install-mongodb-2.png)

 安装完毕有显示，左边图标表示 在终端快速启动Docker 右边图标表示 Kitematic Docker的管理工具

 之后你可以在你的应用里找到这两个应用。

## 安装mongodb

在Kitematic界面搜索mongo

 ![](/assets/install-mongodb-4.png)

 点击“create”按钮就可以下载并安装mongodb的镜像，完成后会自动启动mongodb。


 ![](/assets/install-mongodb-5.png)

 右边出现的端口就是你在程序中要用的，每个人端口可能不一样。

