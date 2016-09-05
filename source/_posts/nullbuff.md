---
title: 利用Method-Forwarding对null值的万金油处理
date: 2016-05-16 15:25:11
categories: iOS组件BuffKit
tags: [Objective-C,iOS组件,BuffKit]
---
方法源码

https://github.com/FlashHand/BuffKit/tree/master/BuffKit/NullBuff

常常会从服务器拿到蛋疼的null值，说不定就导致闪退了，考虑到数据解析后的结果大不了是NSNumber，NSString，NSDictionary，NSArray这四种，我就写了个NSNull的Extension.

像这样：
```
-(NSInteger)length
{
  return 0;
}
-(NSInteger)count
{
  return 0;
}
//...
添加了各种NSNumber，NSString，NSDictionary，NSArray里的对象方法。
```
这样可以足够覆盖实际需求了，但总归是有限的，细想了下决定用消息转发处理：
```
//
// Created by BoWang on 16/5/16.
// Copyright (c) 2016 BoWang. All rights reserved.
//

#import <Foundation/Foundation.h>
//NSString,NSArray,NSDictionary
@interface NSNull (NullBuff)

@end

@interface NullBuff : NSObject
@end
```
```
//
// Created by BoWang on 16/5/16.
// Copyright (c) 2016 BoWang. All rights reserved.
//

#import "NullBuff.h"
#import <objc/runtime.h>

@implementation NSNull (NullBuff)
- (id)forwardingTargetForSelector:(SEL)aSelector {
    //函数说明：class_getInstanceMethod用来获取某个类的某个selector的Method结构体
    //method_copyReturnType用来获取该方法的返回值类型，如果没有该方法则返回空。
    //处理NSNumber,NSString,NSArray,NSDictionary，这里会遍历这四个类，当returnType为真得时候，说明当前遍历到的类含有该selector.
    //由于OC良好的命名机制，同样的selector不会有不同类的返回值。
    NSArray *supporttedTypes = @[@"NSNumber" , @"NSString" , @"NSArray" , @"NSDictionary"];
    for (int i = 0; i < 4; ++i) {
        Method m = class_getInstanceMethod(NSClassFromString(supporttedTypes[i]) , aSelector);
        const char *returnType = method_copyReturnType(m);
        if (returnType) {
            NSString *returnTypeStr = [[NSString alloc] initWithCString:returnType encoding:NSUTF8StringEncoding];
            free(returnType);
            switch (i) {
                case 0:
                    return @(0);
                    break;
                case 1:
                    return @"";
                    break;
                case 2:
                    return @[];
                    break;
                case 3:
                    return @{};
                    break;
                default:
                    break;
            }
        }

    }
    return [super forwardingTargetForSelector:aSelector];
}
@end

@implementation NullBuff {

}
@end
```
原理很简单：NSNull是继承于NSObject，而NSObject的

- (id)forwardingTargetForSelector:(SEL)aSelector 可以被重写。

该方法的可以将消息转发给它的return value.
