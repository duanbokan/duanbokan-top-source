---

title: Linux下搭建Bouncycastle加解密环境

date: 2016-03-03 16:31:09

tags: [ Bouncycast , Https证书生成]

categories: Android

keywords: Bouncycast , Https证书生成

description: Linux下搭建Bouncycastle环境

top_img: http://image.jucaiwy.com/image/20160303/bouncycastle_head.jpeg

cover: http://image.jucaiwy.com/image/20160303/bouncycastle_head.jpeg

---

Bouncycastle是一种Java平台开放源码的轻量级密码术包，可用来管理Android的证书。此次因为需要自己生成Https的证书，因此，专门学习了一下搭建Bouncycastle环境的方法，对学习过程记录如下：

### 1. 环境

我使用的环境信息如下：

- Linux版本：CentOS 5.8 64位操作系统
- Java版本：1.6.0_35


### 2. 搭建步骤

具体搭建步骤记录如下：

#### 2.1 下载Bouncycastle的Jar包

官方网站地址是：[Bouncycastle官网](http://www.bouncycastle.org/latest_releases.html)，网页上有两个可以下载的：

一个是crypto-154.tar.gZ(文件较小，但是我下载了以后，出现无法解压的现象，windows和linux下均无法解压)

另外一个是crypto-154.zip,推荐下载该版本，文件较大，可以直接解压得到jar文件夹，这里因为我的java版本是1.6的所以选择了`bcprov-debug-jdk15on-154.jar`。

或者也可以直接点击下载 [bcprov-jdk15on-146.jar](http://www.bouncycastle.org/download/bcprov-jdk15on-146.jar)

#### 2.2 上传下载的安装包

将下载得到的Jar包上传到linux服务器上，找到java的根目录，将其放在jre/lib/ext/目录下。如果找不到jre的路径，可以使用echo命令进行打印

	echo $JAVA_HOME


![](http://image.jucaiwy.com/image/20160303/bouncycastle1.png)


#### 2.3 配置

修改jre/lib/security/java.security文件，在其provider列表中添加一条：
```
security.provider.10=org.bouncycastle.jce.provider.BouncyCastleProvider
```

安装到此结束

![](http://image.jucaiwy.com/image/20160303/bouncycastle2.png)


### 3. 测试

新建Test.java文件，添加以下代码：
    
    import javax.crypto.Cipher;
    import javax.crypto.KeyGenerator;
    import javax.crypto.SecretKey;
    
    public class Test
    {
    	public static final String stringToEncrypt = "Thisis a test.";
    	
    	public static void main(String[] args) throws Exception
    	{
    		System.out.print("Attemptingto get a Blowfish key...");
    		KeyGenerator keyGenerator = KeyGenerator.getInstance("Blowfish");
    		keyGenerator.init(128);
    		SecretKey key = keyGenerator.generateKey();
    		System.out.println("OK");
    		System.out.println("Attemptingto get a Cipher and encrypt...");
    		Cipher cipher = Cipher.getInstance("Blowfish/ECB/PKCS5Padding");
    		cipher.init(Cipher.ENCRYPT_MODE, key);
    		byte[] cipherText = cipher.doFinal(stringToEncrypt.getBytes("UTF8"));
    		System.out.println("OK");
    		System.out.println("Testcompleted successfully.");
    	}
    }
    

当输出结果为下文时，代表搭建成功

	Attemptingto get a Blowfish key...OK
	Attemptingto get a Cipher and encrypt...
	OK
	Testcompleted successfully.
    





