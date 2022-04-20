---

title: Android自动化测试——Money

date: 2016-06-23 18:34:46

tags: [ Android , 自动化测试 , Monkey]

categories: Android

keywords: Android , 自动化测试 , Monkey

description: Android自动化测试——Money

top_img: http://image.jucaiwy.com/image/20160623/monkey.jpeg

cover: http://image.jucaiwy.com/image/20160623/monkey.jpeg

---


之前有和公司同事一起研究过Android的一些自动化测试工具，当时记得花费了大半天时间去研究怎么使用，但是却忘记整理了，趁着最近不太忙，整理下Android的自动化测试工具吧。

当前最常用的Android自动化测试工具主要有以下几个：

1. Monkey：    [主页地址(Google)](https://developer.android.com/studio/test/monkey.html)

2. MonkeyRunner：  [主页地址(Google)](https://developer.android.com/studio/test/monkeyrunner/index.html)
3. Robotium：    [主页地址(Git)](https://github.com/RobotiumTech/robotium)

这三个中，Monkey和MonkeyRunner均在Google的开发者中心有介绍，上边也有给出连接，需要翻墙，下边将逐个进行介绍。




### 1.Monkey

Monkey是Android系统自带的测试工具，谷歌官方给出的介绍是：Monkey是一款可以运行在虚拟机或是真机上的测试程序，主要原理是通过发送一系列的伪随机流事件，模拟用户点击滑动等操作，测试应用程序的健壮性。


#### 1.1 Monkey命令
Monkey的使用方法比较简单，使用adb连接上客户端以后，可以直接使用Monkey命令进行测试


![](http://image.jucaiwy.com/image/20160623/SiXKOPD.png)


图中红线圈出来的为一些常用命令：

 1. -p					指定应用程序包名
 
 2. --pct-touch			指定输入touch事件所占百分比
 3. --pct-trackball		指定输入轨迹事件所占百分比
 4. -s	种子，生成伪随机数的Seed，两次Monkey的Seed一致时，将产生相同的随机事件		
 5. --throttle			每一次事假与下一次事件之间的延时，单位为毫秒

#### 1.2 Monkey使用方法

为了更加清晰的演示Monkey的随机事件，我写了一个简单的画板，这样每一次输入，将会直观的呈现在屏幕上。画板测试代码：我是测试画板

![](http://image.jucaiwy.com/image/20160623/942rGkm.png)


上图为Monkey基本命令使用方法，分为两步：

1. 使用adb shell 连接客户端
2. 执行monkey命令

其中 

1. **-p** 指定我需要测试的应用的包名：com.xinwei.test.monkey 如果没有安装这个应用，也可以直接使用系统自带的计算器进行测试，更加直观。系统计算器包名:com.android.calculator2

2. **--pct-motion 100** 表示输入事件中动作事件占100%

3. **throttle** 延时 200毫秒，即每一次动作之间均延时200ms

4. **1000** 最后一位 一千次随机事件

当然，也可以不指定包名输入事件等，只需要指定随机事件个数，试试吧，有惊喜的。

### 1.3 测试结果

使用上述命令，测试自定义画板应用，设置输入事件全部为动作事件，结果如下：

![](http://image.jucaiwy.com/image/20160623/itdOHEF.gif)




当没有指定事件类型时，即输入事件全部为随机事件时：




![](http://image.jucaiwy.com/image/20160623/pvaNYeI.gif)





