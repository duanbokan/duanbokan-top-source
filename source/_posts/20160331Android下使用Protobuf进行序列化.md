---

title: Android下使用Protobuf进行序列化

date: 2016-03-31 18:34:46

tags: [ Android , Protobuf]

categories: Android

keywords: Android , Protobuf

description: Android下使用Protobuf进行序列化

top_img: http://image.jucaiwy.com/image/20160331/proto_google.jpg

cover: http://image.jucaiwy.com/image/20160331/proto_google.jpg

---


Protocol Buffer是Google发起的，一个语言无关、平台无关的序列化/反序列化结构化数据的通用机制（框架），可用于通信协议，数据存储等。


近期在做一柬埔寨的小项目，上线后在运行过程中，总是有用户反映应用在网络请求时，速度较慢，影响用户体验。对该问题结合使用环境及代码进行分析，发现存在以下问题：

1. App中使用Json进行数据传输，存在很多冗余字段的传输，而且经过查阅资料，Json对数据进行序列化以后，数据包仍然很大。
2. 柬埔寨存在网络信号问题，很多情况下，信号较差，大数据包的传输存在压力

综合来说，网络环境不稳定和传输数据包过大应该是导致网络请求过程中吃力、或者是超时的主要原因，因此，在优化时，主要考虑如何压缩传输过程中数据包。


### 1. 什么是Protobuf

protobuf，全称：Google Protocol Buffer，是Google开源的一种轻便高效的结构化数据存储格式，可以用于结构化数据的串行化，也称作序列化，主要用于数据存储或是RPC数据交换，支持多语言，可拓展


### 2. 项目主页

protobuf主页：   [主页地址](https://developers.google.com/protocol-buffers/)

protobuf下载(Java)：	 [下载地址](https://developers.google.com/protocol-buffers/docs/downloads#release-packages)

protobuf Github：   [Git地址](https://github.com/google/protobuf)

### 3. 使用
因为Android环境下使用Java语言进行开发，因此我们选择Java版本进行下载。

首先需要到Protobuf主页上下载最新的源码包：[下载地址](https://github.com/google/protobuf/releases/download/v2.6.1/protobuf-2.6.1.tar.gz)

或者是下载Google打包好的Windows下可执行文件：[下载地址](https://github.com/google/protobuf/releases/download/v2.6.1/protoc-2.6.1-win32.zip)

#### 3.1 源码编译

首先，在Eclipse中开发，需要引入protobuf需要的Jar包，而官方只给出了相应源码，因此需要自己进行编译。编译方法如下：

1. 安装Maven环境，具体安装过程，参考网上：[连接](http://jingyan.baidu.com/article/295430f136e8e00c7e0050b9.html)
2. 安装好Maven后，需要进行编译，才能生成需要引入的Jar文件，具体操作为：首先将下载好的protoc.exe文件复制到\protobuf-2.6.1\src目录下。然后进入protobuf-2.6.1\java目录下，直接运行命令：
	
```bash
mvn install
```
如下：

 ![](http://image.jucaiwy.com/image/20160331/D14jNhV.png)

	编译完成后，会在\protobuf-2.6.1\java\target\目录下生成文件：protobuf-java-2.6.1.jar。可以直接导入Eclipse中进行使用

#### 3.2 构建对象
即定义数据模型，需要新建一个文件，后缀名为.proto,例如，我需要生成对象如下：
	
	public class ProductInfo
	{
		private String productID;
		
		private String productName;
		
		private String productAmount;
		
		private String productDate;
		
		private String productMessage;
	}


需要按照以下步骤进行

1. 新建文件，名为：ProductInfo.proto
2. 使用UE或者NotePad++等编辑软件打开该文件，对需要录入的字段进行编辑，如下(UE直接写出来，太难排版了，只能上图)

	![](http://image.jucaiwy.com/image/20160331/7hiJIcM.png)
	
说明：该文件中：

- java_package：包名
- java_outer_classname：类名
- ProductInfo：对象名	
- required：必须有值
- optional：可以不写值
- string：相当于Java中的String
- int32：相当于Java中的int

具体proto文件类型、Java类型及C++类型对比如下：

![](http://image.jucaiwy.com/image/20160331/hF1WG8K.png)	

具体还可以参考连接： [ Protobuf语言指南](http://www.cnblogs.com/dkblog/archive/2012/03/27/2419010.html)

3. 使用protoc进行编译，语句为：

	**protoc --java_out=E:\Android开源项目\Protobuf数据传输协议\protobuf ProductInfo.proto**

注：
	

- protoc：指使用下载好的protoc.exe进行编译

- java_out:编译器编译时，生成的语言同样的参数也可以是cpp_out

- "=" 后边内容为需要编译的文件路径和文件名

	编译完成后，会在编译文件位置相同的路径下，生成编译完成的文件，Java的以包的形式存放，即以文件夹的形式存放。

至此，已经完成了Jar包的编译和需要传输的对象的编译，接下来举例说明如何进行使用。

#### 3.3 在Java中使用

新建Android工程，将生成的Jar文件拷贝到工程目录下的libs文件夹里边，将生成的模型文件拷贝到工程中对应的包下边，可以进行创建和使用对象：

示例代码：

```java
private void testProto() {
    ProductInfo.Product.Builder builder = ProductInfo.Product.newBuilder();
    builder.setAmount("100");
    builder.setUid(10010);
    builder.setProductCode("Product1");
    builder.setLuckNum("12345");
    builder.setAllPhoneNum("10086");

    // 生成product对象
    ProductInfo.Product product = builder.build();

    // 转换为Byte
    byte[] products = product.toByteArray();

    // 反解
    try {
         Product productParse = ProductInfo.Product.parseFrom(products);
    } catch (InvalidProtocolBufferException e) {
         e.printStackTrace();
    }
}
```
