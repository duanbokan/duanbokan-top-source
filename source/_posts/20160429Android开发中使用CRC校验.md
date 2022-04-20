---

title: Android使用CRC校验

date: 2016-04-29 18:34:46

tags: [ Android , CRC]

categories: Android

keywords: Android , CRC

description: Android使用CRC校验

top_img: http://image.jucaiwy.com/image/20160429/crc.jpeg

cover: http://image.jucaiwy.com/image/20160429/crc.jpeg

---


最近手头的一个项目，客户端和服务端之间进行通讯时，由于受到当地网络等问题的限制，使用Json传输，数据内容过于庞大，需要对该信息进行压缩，以便实现快速传输。这里使用Byte数组进行传输，前后台对协议进行商定，而后编码、传输、解码实现通信，传输过程中，为保证传输内容的完整性，需要使用CRC进行校验。


### CRC概念——百度百科
CRC即循环冗余校验码（Cyclic Redundancy Check[1]  ）：是数据通信领域中最常用的一种查错校验码，其特征是信息字段和校验字段的长度可以任意选定。循环冗余检查（CRC）是一种数据传输检错功能，对数据进行多项式计算，并将得到的结果附在帧的后面，接收设备也执行类似的算法，以保证数据传输的正确性和完整性。


### CRC编码

编码：根据传输内容逐位计算，得到CRC编码，附加在该Byte数组后边。 

```java
	/**
     * 为Byte数组添加两位CRC校验
     *
     * @param buf
     * @return
     */
    public static byte[] setParamCRC(byte[] buf) {
        int MASK = 0x0001, CRCSEED = 0x0810;
        int remain = 0;

        byte val;
        for (int i = 0; i < buf.length; i++) {
            val = buf[i];
            for (int j = 0; j < 8; j++) {
                if (((val ^ remain) & MASK) != 0) {
                    remain ^= CRCSEED;
                    remain >>= 1;
                    remain |= 0x8000;
                } else {
                    remain >>= 1;
                }
                val >>= 1;
            }
        }

        byte[] crcByte = new byte[2];
        crcByte[0] = (byte) ((remain >> 8) & 0xff);
        crcByte[1] = (byte) (remain & 0xff);

        // 将新生成的byte数组添加到原数据结尾并返回
        return concatAll(buf, crcByte);
    }

```


### CRC解码

在服务端接受到客户端传输过来的byte数组，然后先进行CRC校验，通过后，才能确定数据在传输过程中未发生改变，才能进行下一步业务操作。

```java
    /***
	 * CRC校验是否通过
	 * 
	 * @param srcByte
	 * @param length
	 * @return
	 */
	public static boolean isPassCRC(byte[] srcByte, int length)
	{
		// 取出除crc校验位的其他数组，进行计算，得到CRC校验结果
		int calcCRC = calcCRC(srcByte, 0, srcByte.length - length);
		
		// 取出CRC校验位，进行计算
		int receive = toInt(getBytesByindex(srcByte, srcByte.length - length, srcByte.length - 1));
		
		// 比较
		return calcCRC == receive;
	}
	
```

在这中间  计算CRC的方法和之前的基本是一致的。只是第二步中，取到两位CRC校验值以后，将其转为int，然后进行比较


### Demo

这里有一个我写的CRC校验的Demo，没有使用jar包，可以进行修改。

[CRC校验Demo](http://download.csdn.net/detail/duanbokan/9506690)



















