### Docker 

在本地安装数据库时都会污染电脑的环境，避免这种情况发生可以采用docker容器的方式来安装数据库

Docker是一个应用容器引擎，开发者可以打包他们的应用及依赖包到一个可移植的容器中，也可以实现虚拟化，容器使用沙箱机制，因此不会污染电脑环境。详细内容参见[Docker百科](https://baike.baidu.com/item/Docker/13344470?fr=aladdin),更准确的定义参见[docker官网](https://www.docker.com/#/production)

为了更方便的安装和使用Docker，推荐使用Kitematic

### Kitematic

Kitematic 作用是方便你在Mac或Windows上使用Docker，是与Docker Toolbox捆绑在一起的传统解决方案

- 可以自动安装设置Docker
- 提供直观的图形用户界面
- Kitematic与Docker Machine集成 提供VirtualBox VM，在本地安装Docker引擎

### 1. Kitematic安装
进入官方[下载链接](https://www.docker.com/products/docker-toolbox)页面，根据您的系统，选择Mac或Windows版本的进行下载

 ![](https://raw.githubusercontent.com/wiki/MyColourfulLife/dockerLearn/download.png)

因为下载比较慢，我存了一份mac版的放在了百度云，有需要的可以[点击链接](http://pan.baidu.com/s/1dFtms0L)下载 密码:`umxh`


下载完成打开一路继续就安装好了。
 
 ![](https://raw.githubusercontent.com/wiki/MyColourfulLife/dockerLearn/install.png)

 安装完毕有显示，左边图标表示 在终端快速启动Docker 右边图标表示 Kitematic Docker的管理工具

 之后你可以在你的应用里找到这两个应用。

### 2. 简单部署下，使能正常使用

这里是个[官方文档](https://docs.docker.com/get-started/#a-brief-explanation-of-containers)，跟着试试就可以了。

我直接在终端输入
```
docker run hello-world 
```
报错，连不上虚拟机啥的。。。。

还好应用中有个 在终端快速启动Docker的应用 名字叫做**Docker QuickerStart Term**

直接点它就好，它自己会去创建虚拟机进行一系列的部署。

中间有一点点的不顺，就是说boot2docker.iso过期了，

Docker QuickerStart Term这个应用打开的终端会去自动下载，但感觉好慢，

我根据提示用迅雷下载了，然后放到提示的指定目录下了。

这个boot2docker.iso在上面的百度云链接里也有。

然后 再次启动 Docker QuickerStart Term 就顺利的自己部署了。

然后 执行了
```
docker run hello-world
```

首先是说没有这么一个image 
```
Unable to find image 'hello-world:latest' locally
```
然后 就自己去pull代码了。

latest: Pulling from library/hello-world

最后就顺利执行了

Hello from Docker!
This message shows that your installation appears to be working correctly.

这表明 安装是没问题的。

---

为了生成上面信息，这个工具其实还是做了很多的：

 1.  The Docker client contacted the Docker daemon.
 2.  The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3.  The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4.  The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

