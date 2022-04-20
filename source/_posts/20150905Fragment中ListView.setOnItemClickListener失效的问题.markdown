---

title: Fragment中ListView.setOnItemClickListener失效的问题

date: 2015-09-15 16:01:19

tags: [Android , 问题记录 ]

categories: [Android , 问题记录 ]

keywords: Android , 问题记录 

description: Fragment中ListView.setOnItemClickListener失效的问题

top_img: http://image.jucaiwy.com/image/20150905/android.jpg

cover: http://image.jucaiwy.com/image/20150905/android.jpg

---

  今天在写代码的时候发现一个很奇怪的现象，当一个Activity继承自Fragment时，在其中设置ListView.setOnItemClickListener会没有作用，不会抛出异常，打断点也进不去，没有找到原因，但是找到了解决方案：
 
在ListView的Item的Layout代码最外层的Layout中设置：

	android:clickable="false"
	android:focusable="false"
 	
 