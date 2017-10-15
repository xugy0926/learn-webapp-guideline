## Kitematic 


Docker是一个应用容器引擎，开发者可以打包他们的应用及依赖包到一个可移植的容器中，也可以实现虚拟化，容器使用沙箱机制，因此不会污染电脑环境。

(https://baike.baidu.com/item/Docker/13344470?fr=aladdin),更准确的定义参见[docker官网](https://www.docker.com/#/production)

为了更方便的安装和使用Docker，推荐使用Kitematic客户端。

Kitematic 作用是方便你在Mac或Windows上使用Docker，是与Docker Toolbox捆绑在一起的传统解决方案

- 可以自动安装设置Docker
- 提供直观的图形用户界面
- Kitematic与Docker Machine集成 提供VirtualBox VM，在本地安装Docker引擎

#### 下载Kitematic
进入官方[下载链接](https://www.docker.com/products/docker-toolbox)页面，根据您的系统，选择Mac或Windows版本的进行下载

 ![](/assets/install-mongodb-1.png)

因为下载比较慢，我存了一份mac版的放在了百度云，有需要的可以[点击链接](http://pan.baidu.com/s/1dFtms0L)下载 密码:`umxh`

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

