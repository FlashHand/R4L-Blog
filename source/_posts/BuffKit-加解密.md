---
title: iOS加解密工具(AES,DES,3DES,BLOWFISH)
date: 2016-04-12 19:46:22
categories: iOS组件BuffKit
tags: [Objective-C,iOS组件,BuffKit]
---
加解密工具CryptoBuff包含了：

对NSData的AES,DES,3DES,BLOWFISH加密解密方法。

以及对NSData和NSString的MD5，SHA1,SHA2的计算。

BuffKit源码及Demo地址：

https://github.com/FlashHand/BuffKit

以Blowfish加解密为例子：
```
NSString *sourceStr=@"12345678901234561";
NSData *source=[sourceStr dataUsingEncoding:NSUTF8StringEncoding];
[source bfCryptoBlowFishEncodeWithMode:BuffCryptoModeCFB padding:YES iv:@"1234567890" key:@"1234567890" completion:^(NSData *cryptoData) {
       [cryptoData bfCryptoBlowFishDecodeWithMode:BuffCryptoModeCFB padding:NO iv:@"1234567890" key:@"1234567890" completion:^(NSData *cryptoData2) {
           NSString *result = [[NSString alloc] initWithData: cryptoData2 encoding: NSUTF8StringEncoding];
           NSLog(@"%@",result);
       }];
   }];

```
上面包含了encode和decode过程。<br>
打印结果为：12345678901234561

#### 参数说明:
**BuffCryptoMode** 为加密模式，支持的加密模式有
```
typedef NS_ENUM(NSInteger, BuffCryptoMode) {
    BuffCryptoModeECB		= 1,//kCCModeECB
    BuffCryptoModeCBC		= 2,//kCCModeCBC
    BuffCryptoModeCFB		= 3,//kCCModeCFB
    BuffCryptoModeCTR		= 4,//kCCModeCTR
    BuffCryptoModeOFB		= 7,//kCCModeOFB
    BuffCryptoModeCFB8		= 10,//kCCModeCFB8
};
```
**isPadding** 表示是否填充，YES为添加填充，注意在ECB和CBC模式下，padding必须为NO。<br>
**iv** 为初始化偏移量（initialization vector）ECB和CBC下填写会被忽略，

注意iv的length必须和key的length相同

**key** 是密钥，AES加密时key的长度必须是16，24，32其中一个，

DES加密时是8，3DES加密时是24，

blowfish加密时key的长度可以是整数区间[8,56]中的任意一个.

加密过程是在GLOBAL_QUEUE里执行的，**completion** block是异步回调,

block传入一个NSData类的参数cryptoData，加密或解密失败时cryptoData=nil.

注意：加密时，内容的长度若不能对key.length进行整除，则会对内容进行补位，

否则加密就会失败。
```
NSMutableData *sourceM=[[NSMutableData alloc]initWithData:source];
while (sourceM.length % key.length != 0) {
    [sourceM appendData: [@"\0" dataUsingEncoding: NSASCIIStringEncoding]];
}
```
加解密和MD5，SHA1,SHA2的计算主要依赖于 **CommonCrypto** 库

若需要自定义多线程加解密和了解实现方式可以看源码

https://github.com/FlashHand/BuffKit
