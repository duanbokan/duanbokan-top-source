---

title: HarmonyOS开发—基础—环境搭建

date: 2023-12-22 14:54:46

tags: [HarmonyOS开发 , 环境搭建]

categories: HarmonyOS

keywords: HarmonyOS , HarmonyOS开发 , HarmonyOS入门 , HarmonyOS环境搭建

description: HarmonyOS开发

top_img: http://image.jucaiwy.com/image/20240710/harmonyOS_logo.png

cover: http://image.jucaiwy.com/image/20240710/harmonyOS_logo.png

---

## HarmonyOS开发—基础—环境搭建


### 一、DevEco Studio下载

DevEco Studio是基于IntelliJ IDEA Community开源版本打造，是华为提供的面向全场景多设备，提供一站式的分布式应用开发平台。支持分布式多端开发、分布式多端调测、多端模拟仿真以及全方位的质量与安全保障。我们可以在华为官网上下载自己所需要的开发工具，一般情况下，开发工具版本和HarmonyOS版本一一对应。



这里最新的是DevEco Studio 3.1。配套支持HarmonyOS 3.1版本及以上的应用及服务开发，提供了代码智能编辑、低代码开发、双向预览等功能，以及轻量构建工具DevEco Hvigor 、本地模拟器，持续提升应用及服务开发效率。下载链接：https://developer.huawei.com/consumer/cn/deveco-studio/



需要注意的是：DevEco Studio的编译构建依赖JDK，DevEco Studio预置了OpenJDK，版本为1.8，安装过程中会自动安装JDK。

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudio%E7%AE%80%E4%BB%8B.png)

下载完成以后，双击安装。

### 二、DevEco Studio配置

DevEcoStudio安装完成后，首次进入需要安装Node.js和ohpm。

**Node.js：**一个运行在Chrome V8引擎的JavaScript运行环境。现在被广泛的运用于Web前端工程化构建中。由于鸿蒙的Ark UI框架是基于Node.js进行开发的，所以像绝大多数Web前端开发框架一样，首先要在当前操作系统中安装Node.js框架。新版本的DevEco Studio已经集成了node.js的安装，无需前置安装。

**ohpm：**openharmony package manager，鸿蒙生态三方库的包管理工具，支持OpenHarmony共享包的发布、安装和依赖管理，同样在新版本的DevEco Studio中集成了安装包。

这里只需要在首页单击Agree进入配置，配置node.js和ohpm的安装源，即可。推荐选择从华为镜像直接下载安装，选择本地安装路径。

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioBasic.png)

Node.js和ohpm选择好以后，点击下一步，进入SDK Setup页面，选择和修改SDK安装位置：

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioSDK.png)

这里需要点击Next以后，需要阅读和勾选License协议，再次点击下一步进入预览页面，确认各项下载路径无误以后，点击Next开始进行下载安装。

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioSummary.png)

### 三、DevEco Studio主题

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioTheme.png)

### 四、DevEco Studio HelloWord

安装和配置完成以后，就可以开始我们的第一个项目啦，当然必须是HelloWorld。通过Create Project 创建一个Empty Ability。

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioCreate.png)

项目名称叫做HelloWorld，同步通过以后，可以进入HelloWorld工程中，默认是Project模式，定位到我们的Index.ets文件上。

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioProject.png)

这里我们也可以通过右上角目录视图切换，切换至Ohos视图：

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioIndex.png)

这里分别对这三个目录进行介绍：

AppScope：主要用于存放整个应用公共的信息与资源

entry：是默认的初始模块，其中ets中存放编写的代码文件，ets中page目录，则用来存放我们的页面文件，例如初始的index.ets就存放在ets->pages目录下；configuration中存放相应模块的配置文件；resources中存放资源文件

configuration：工程、应用级别的配置文件

**预览：**右侧Previewer点击后，可以实时预览我们编写的页面在手机上的样式：

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioPreView.png)

创建模拟器：通过菜单栏中Tools---DeviceManager打开这个页面，也可以通过右上角Device区域的Device Manager打开模拟器管理页面。选择好模拟器创建位置后，选择+号，可以新建本地模拟器。

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioDevice.png)

第一次安装系统后，会提示下载安装模拟器，根据提示下载安装即可，然后就可以选择创建。

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioHardWare.png)

安装完成后，可以一键运行：至此，Hello World工程，成功运行起来：

![img](http://image.jucaiwy.com/image/20240710/DevEcoStudioHello.png)   
