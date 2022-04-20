---

title: Android自动化测试——MoneyRunner

date: 2016-06-23 18:34:46

tags: [ Android , 自动化测试 , MonkeyRunner]

categories: Android

keywords: Android , 自动化测试 , MonkeyRunner

description: Android自动化测试——MoneyRunner

top_img: http://image.jucaiwy.com/image/20160623/monkey_runner.jpeg

cover: http://image.jucaiwy.com/image/20160623/monkey_runner.jpeg

---


接着上一篇文章继续写，上篇文章中，主要对[Android自动化测试——Monkey](http://blog.csdn.net/duanbokan/article/details/51742893)的概念和使用方法进行了简单介绍，，这篇文章中，我们继续探讨MonkeyRunner的使用

有人之前问过我，Monkey和MonkeyRunner之间有什么区别，我用这样一个比喻来回答他，Monkey就是一只猴子，放养的猴子，未经训练的猴子，所以它在测试的时候，只会胡乱点、胡乱划(随机事件)；而MonkeyRunner则不同于Monkey，它属于家养的，经过训练的，可以按照主人的命令，做出各种动作(自定义事件)。

ok，下边直接讲如何使用MonkeyRunner


### 1.1 MonkeyRunner环境搭建
搭建MonKeyRunner环境需要以下三部分：

1. Java环境，及Java环境变量配置

2. Android SDK Monkey是Google提供的自动化测试工具，在SDK根路径的tools文件夹中可以找到，因此，使用MonkeyRunner需要下载安装SDK。 下载完成后，直接解压，绿色，无需安装，注意解压路径不能有中文或空格。
	
	[SDK下载路径](http://www.android-doc.com/sdk/index.html)

3. 安装Python编译环境  ：Python用于支持MonkeyRunner的运行，安装Python，同时配置其环境变量。

	[Python下载路径](https://www.python.org/downloads/)

检查Python和MonkeyRunner是否配置正确：

![](http://i.imgur.com/liqPDX8.png)



### 1.2 录制脚本

与其他自动化测试工具相似，MonkeyRunner也可以进行脚本录制。这里需要使用Python命令打开MonkeyRunner脚本录制界面：

**第一步**： 新建文本文档，命名为record.py，编辑其内容：

```python
from com.android.monkeyrunner import MonkeyRunner as mr  
from com.android.monkeyrunner.recorder import MonkeyRecorder as recorder  
      
device = mr.waitForConnection()  
recorder.start(device) 
```
编辑完成后，将该文件放在monkeyrunner平级的目录下(这里注明放在和SDK平级的目录下，是为了方便执行，不用输入绝对路径)，即 **E:\Android\sdk\tools**(注意自己的SDK路径)


**第二步**： 在cmd命令行，执行该文件：


![](http://image.jucaiwy.com/image/20160623/monkey/glI0yVI.png)



执行成功后，会出现MonkeyRunner的录制界面：


![](http://image.jucaiwy.com/image/20160623/monkey/2wgySa6.png)

界面中：

1. 左侧为当前屏幕界面
2. 右侧为当前操作类型及位置
3. 标题栏中：
	1. Wait:设置输入时间间隔，单位为秒
	2. Press a Button: 硬件按钮，包括menu、home、search，以及对按钮的press、down、up属性
	3. Type Something: 输入内容
	4. Fling: 滑动输入，可设置方向和操作范围
	5. Export Actions:导出录制脚本
	6. Refresh Display:界面刷新，或者叫做界面同步

至此，已经学会了如何使用MonkeyRunner进行脚本录制，接下来将如何回放录制的脚本。
我们仍然使用之前的自定义画板进行录制（因为之前画板代码存在问题，没有绘制点，只是绘制线，因此需要输入滑动事件），我们使用MonkeyRecoder的Fling功能，随便绘制两条线，结果如下：


![](http://image.jucaiwy.com/image/20160623/monkey/BzLeUro.png)

左侧为绘制线条，右侧为动作，录制完成后，可以将脚本导出并保存。

### 1.3 脚本回放

使用MonkeyRecoder录制完脚本后，可以对脚本进行回放。


回放时，需要使用MonkeyRunner执行Python脚本，内容固定：

```python
import sys
from com.android.monkeyrunner import MonkeyRunner
CMD_MAP = {
'TOUCH': lambda dev, arg: dev.touch(**arg),
'DRAG': lambda dev, arg: dev.drag(**arg),
'PRESS': lambda dev, arg: dev.press(**arg),
'TYPE': lambda dev, arg: dev.type(**arg),
'WAIT': lambda dev, arg: MonkeyRunner.sleep(**arg)
}
		
# Process a single file for the specified device.
def process_file(fp, device):
for line in fp:
(cmd, rest) = line.split('|')
try:
# Parse the pydict
rest = eval(rest)
except:
print 'unable to parse options'
continue
		
if cmd not in CMD_MAP:
print 'unknown command: ' + cmd
continue
CMD_MAP[cmd](device, rest)
def main():
file = sys.argv[1]
fp = open(file, 'r')
device = MonkeyRunner.waitForConnection()
process_file(fp, device)
fp.close();
		
if __name__ == '__main__':
main()
```

同样的操作，新建python文档，将这个脚本粘贴至该文档中，文档路径与MonkeyRunner路径相同(避免使用绝对路径进行操作)

然后执行该脚本，得到结果与之前绘制效果一致。

![](http://image.jucaiwy.com/image/20160623/monkey/GluioC1.gif)




### 1.4 自定义脚本

对之前录制脚本文件使用编辑器打开，会发现，生成脚本其实就是一系列语句，所以我们可以对该脚本进行修改，完成自定义，下面我们通过修改脚本，绘制一个正方形，修改后脚本如下：

```python
WAIT|{'seconds':5.0,} 
DRAG|{'start':(100,100),'end':(100,1000),'duration':1.0,'steps':10,}
DRAG|{'start':(100,1000),'end':(1000,1000),'duration':1.0,'steps':10,}
WAIT|{'seconds':1.0,} 
DRAG|{'start':(1000,1000),'end':(1000,100),'duration':1.0,'steps':10,}
DRAG|{'start':(1000,100),'end':(100,100),'duration':1.0,'steps':10,}
```

回放该脚本：

![](http://image.jucaiwy.com/image/20160623/monkey/6KGGXc2.gif)

这里仅仅只是用了DRAG和WAIT两个动作。同样的还有很多：

```python
PRESS|{'name':'MENU','type':'downAndUp',}
PRESS|{'name':'HOME','type':'downAndUp',}
PRESS|{'name':'SEARCH','type':'downAndUp',}
PRESS|{'name':'MENU','type':'down',}
TYPE|{'message':'hello',}
TOUCH|{'x':469,'y':836,'type':'downAndUp',}
```

## 2. 结束

至此已经大致讲完了MonkeyRunner的基本使用方法，至于后边想到什么了，记得使用MonkeyRunner时有个takePicture的命令，想起来了再进行补充吧。


