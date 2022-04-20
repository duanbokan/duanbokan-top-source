---
layout: post
title: "Android环境String.xml文件中空格的处理"
date: 2015-02-02 16:59:25
comments: true
tags: 
	- String.xml空格
	- 问题记录
---

 Android的String.xml文件中将多个空格合并为一个空格了，例如：

	<string name="test">test      test      test      test</string>

每一个Test中间有五个空格，但是实际在Log中打印出来的：

	![String.xml的Log](http://img.blog.csdn.net/20150202170214171)

为解决这个问题，需要在xml文件中将空格用Unicode 字符值： **\u0020** 代替：

	<string name="test">test\u0020\u0020\u0020\u0020\u0020test\u0020\u0020\u0020\u0020\u0020test\u0020\u0020\u0020\u0020\u0020test</string>
