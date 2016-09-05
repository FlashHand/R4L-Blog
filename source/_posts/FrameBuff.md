---
title: UIView,CALayer,UIImage的尺寸或位置的快捷获取
date: 2016-05-12 16:51:27
categories: iOS组件BuffKit
tags: [Objective-C,iOS组件,BuffKit]
---
快捷地获取UIView，CALayer的位置和尺寸以及UIImage的尺寸





```
#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>
@interface UIView (FrameBuff)
//边界
@property (nonatomic)CGFloat left;
@property (nonatomic)CGFloat top;
@property (nonatomic)CGFloat right;
@property (nonatomic)CGFloat bottom;
//宽高
@property (nonatomic)CGFloat width;
@property (nonatomic)CGFloat height;

//原点和尺寸
@property (nonatomic)CGPoint origin;
@property (nonatomic)CGSize size;

//原点坐标
@property (nonatomic)CGFloat x;
@property (nonatomic)CGFloat y;

//中心坐标
@property (nonatomic)CGFloat centerX;
@property (nonatomic)CGFloat centerY;

//中心相对原点的位置
@property (nonatomic,readonly)CGPoint midPoint;
@property (nonatomic,readonly)CGFloat midX;
@property (nonatomic,readonly)CGFloat midY;

@end
@interface CALayer (FrameBuff)
//边界
@property (nonatomic)CGFloat left;
@property (nonatomic)CGFloat top;
@property (nonatomic)CGFloat right;
@property (nonatomic)CGFloat bottom;
//宽高
@property (nonatomic)CGFloat width;
@property (nonatomic)CGFloat height;

//尺寸
@property (nonatomic)CGSize size;

//原点坐标(position.x,position.y,zPosition)
@property (nonatomic)CGFloat x;
@property (nonatomic)CGFloat y;
@property (nonatomic)CGFloat z;

//中心相对原点的位置
@property (readonly)CGPoint midPoint;
@property (readonly)CGFloat midX;
@property (readonly)CGFloat midY;
@end
@interface UIImage (FrameBuff)
@property (readonly)CGFloat width;
@property (readonly)CGFloat height;
@end
@interface FrameBuff : NSObject

@end

```
扩展的实现
```
#import "FrameBuff.h"

#pragma mark - UIView

@implementation UIView (FrameBuff)
#pragma mark 边界

- (CGFloat)left {
    return self.frame.origin.x;
}

- (void)setLeft:(CGFloat)left {
    self.x = left;
}

- (CGFloat)top {
    return self.frame.origin.y;
}

- (void)setTop:(CGFloat)top {
    self.y = top;
}

- (CGFloat)right {
    return self.frame.origin.x + self.frame.size.width;
}

- (void)setRight:(CGFloat)right {
    self.x = right - self.width;
}

- (CGFloat)bottom {
    return self.frame.origin.y + self.frame.size.height;
}

- (void)setBottom:(CGFloat)bottom {
    self.y = bottom - self.height;
}

#pragma mark 宽高

- (CGFloat)width {
    return self.frame.size.width;
}

- (void)setWidth:(CGFloat)width {
    self.frame = CGRectMake(self.x , self.y , width , self.height);
}

- (CGFloat)height {
    return self.frame.size.height;
}

- (void)setHeight:(CGFloat)height {
    self.frame = CGRectMake(self.x , self.y , self.width , height);
}

#pragma mark 原点和尺寸

- (CGPoint)origin {
    return self.frame.origin;
}

- (void)setOrigin:(CGPoint)origin {
    self.frame = CGRectMake(origin.x , origin.y , self.width , self.height);
}

- (CGSize)size {
    return self.frame.size;
}

- (void)setSize:(CGSize)size {
    self.frame = CGRectMake(self.x , self.y , size.width , size.height);
}

#pragma mark 原点坐标

- (CGFloat)x {
    return self.frame.origin.x;
}

- (void)setX:(CGFloat)x {
    self.frame = CGRectMake(x , self.y , self.width , self.height);
}

- (CGFloat)y {
    return self.frame.origin.y;
}

- (void)setY:(CGFloat)y {
    ;
    self.frame = CGRectMake(self.x , y , self.width , self.height);
}

#pragma mark 中心坐标

- (CGFloat)centerX {
    return self.x + self.midX;
}

- (void)setCenterX:(CGFloat)centerX {
    self.x = centerX - self.midX;
}

- (CGFloat)centerY {
    return self.y + self.midY;
}

- (void)setCenterY:(CGFloat)centerY {
    self.y = centerY - self.midY;
}

#pragma mark 中心相对于原点位置，readonly

- (CGPoint)midPoint {
    return CGPointMake(self.width / 2 , self.height / 2);
}

- (CGFloat)midX {
    return self.width / 2;
}

- (CGFloat)midY {
    return self.height / 2;
}
@end

#pragma mark - CALayer

@implementation CALayer (FrameBuff)
#pragma mark 边界
- (CGFloat)left {
    return self.x - self.width / 2;
}

- (void)setLeft:(CGFloat)left {
    [self setX:left + self.width / 2];
}

- (CGFloat)top {
    return self.y - self.height / 2;
}

- (void)setTop:(CGFloat)top {
    [self setY:top + self.height / 2];
}

- (CGFloat)right {
    return self.x+self.width/2;
}

- (void)setRight:(CGFloat)right {
    [self setX:right-self.width/2];
}

- (CGFloat)bottom {
    return self.y+self.height/2;
}

- (void)setBottom:(CGFloat)bottom {
    [self setY:bottom-self.height/2];
}

#pragma mark 边界
- (CGFloat)width {
    return self.bounds.size.width;
}

- (void)setWidth:(CGFloat)width {
    [self setBounds:CGRectMake(0,0,self.height,width)];
}

- (CGFloat)height {
    return self.bounds.size.height;
}

- (void)setHeight:(CGFloat)height {
    [self setBounds:CGRectMake(0,0,self.width,height)];
}

- (CGSize)size {
    return self.bounds.size;
}

- (void)setSize:(CGSize)size {
    [self setBounds:CGRectMake(0,0,size.width,size.height)];
}

#pragma mark 位置
- (CGFloat)x {
    return self.position.x;
}

- (void)setX:(CGFloat)x {
    [self setPosition:CGPointMake(x , self.y)];
}

- (CGFloat)y {
    return self.position.y;
}

- (void)setY:(CGFloat)y {
    [self setPosition:CGPointMake(self.x,y)];
}

- (CGFloat)z {
    return self.zPosition;
}

- (void)setZ:(CGFloat)z {
    [self setZPosition:z];
}

#pragma mark 中心相对于左上角的坐标
- (CGPoint)midPoint {
    return CGPointMake(self.width/2,self.height/2);
}


- (CGFloat)midX {
    return self.width/2;
}

- (CGFloat)midY {
    return self.height/2;
}

@end
@implementation UIImage (FrameBuff)
- (CGFloat)width {
    return self.size.width;
}

- (CGFloat)height {
    return self.size.height;
}

@end
@implementation FrameBuff

@end
```
