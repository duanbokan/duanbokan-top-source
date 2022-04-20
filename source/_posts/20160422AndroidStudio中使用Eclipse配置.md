---

title: 在AndroidStudio中使用Eclipse下的CodeFormates和CodeTemplates

date: 2016-04-22 18:34:46

tags: [ Android , Eclipse]

categories: Android

keywords: Android , Eclipse

description: 在AndroidStudio中使用Eclipse下的CodeFormates和CodeTemplates

top_img: http://image.jucaiwy.com/image/20160422/AndroidStudio.jpeg

cover: http://image.jucaiwy.com/image/20160422/AndroidStudio.jpeg

---

换Eclipse为AndroidStudio后，需要将之前Eclipse中的CodeStyle和Formatter导入AndroidStudio中使用。具体操作步骤如下：

### 导入Formatter

想要在AndroidStudio中使用Eclipse风格的Formatter，首先需要安装插件：

1. 点击File——>Setting

![](http://i.imgur.com/7hxJGk8.png)


2. 在打开的Setting界面中，点击Plugins，然后在搜索框中输入Eclipse Code Formatter，搜索结果中点击安装（因为我之前已经安装过了，所以这里只显示Uninstall）

![](http://i.imgur.com/yhdJ8iy.png)

3. 安装完毕后，再次打开File——>Setting，会出现Eclipse Code Formatter 选项，一般在OtherSetting中，若没有可以使用上边的搜索框进行搜索。
4. 打开后，选择 Use the Eclipse code formatter 然后在下边的Eclipse Java Formatter config file中设置自己Formatter的文件路径，然后点击Apply，即可生效。

![](http://i.imgur.com/tL8oWxu.png)


5. 配置完成后，需要进行激活才能使用Eclipse的Formatter，如图，将该图标切换至Eclipse图标，即表示当前使用Eclipse进行Formatter。且每一次Formatter，均会打印一条Log

![](http://i.imgur.com/j3Xu95R.png)



### 使用CodeTemplates

找了半天，都没有找到直接导入Eclipse下CodeTemplates文件的方法，所以，只能自己动手去写了，现在只是找到了，如何为新建的java类添加注释，其余方法，愿大家共同探讨。

![](http://i.imgur.com/khPnucm.png)

这里，需要说明一下，不知道是不是我打开的方式不对，这里添加CopyRight后，需要每一次都Generate一下，才会在文件中生成，不知道有没有自动Generate的，这样就不需要添加HeadFile了


### 直接导入

除了上述两个方法之外，当然也可以直接导入我自己已经配置好的配置文件，文件会在附件中出现，如图，进行导入


![](http://i.imgur.com/dVHhUKU.png)




当然导入时，可以不选择UI之类的东西，因为个人喜好么。

