--- 
layout:     post                      
title:      iOS使用UICollectionView实现可旋转菜单
subtitle:   iOS使用UICollectionView实现可旋转菜单
keywords:   ios,UICollectionView,旋转菜单
date:       2017-01-01               
author:     HuberyYang                
header-img: img/post-bg-desk.jpg  
catalog:    true                     
tags:                             
    - iOS
    - OC
---

1.由于UICollectionVIew是依赖于UICollectionViewFlowLayout进行布局，而UICollectionViewFlowLayout 继承自 UICollectionViewLayout，所以如果我们想进行个性化布局，可以新建一个UICollectionViewLayout的子类来进行个性化布局。

```objc
#import <UIKit/UIKit.h>

@interface YMLRotationLayout : UICollectionViewLayout


/** 按钮半径 */
@property (assign, nonatomic) CGFloat itemRadius;

/** 按钮中心相对菜单中心旋转角度*/
@property (assign, nonatomic) CGFloat rotationAngle;

@end
```

```objc
#import "YMLRotationLayout.h"

@implementation YMLRotationLayout{
    
    NSMutableArray * _attributeAttay;
    CGFloat _rLength;
    NSInteger _itemCount;
}

- (void)prepareLayout{
    [super prepareLayout];
    
    // 按钮个数
    
    _itemCount = (int)[self.collectionView numberOfItemsInSection:0];
    _attributeAttay = [[NSMutableArray alloc] init];
    // 先设定大圆的半径 取长和宽最短的
    
    CGFloat radius = MIN(self.collectionView.frame.size.width, self.collectionView.frame.size.height) / 2.2;
    // 圆心位置
    
    CGPoint center = CGPointMake(self.collectionView.frame.size.width / 2.0, self.collectionView.frame.size.height / 2.0);
    
    _rLength = _itemRadius;
    
    // 设置每个item的大小
    
    for (int idx = 0; idx < _itemCount; idx ++) {
        
        NSIndexPath *indexPath = [NSIndexPath indexPathForItem:idx inSection:0];
        UICollectionViewLayoutAttributes * attris = [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:indexPath];
        // 设置item大小
        
        attris.size = CGSizeMake(_rLength, _rLength);
        
        if (_itemCount == 1) {            
            attris.center = self.collectionView.center;
        } else {
            
            // 计算每个item中心的坐标
            
            // 算出的x，y值还要减去item自身的半径大小
            
            float x = center.x + cosf(2 * M_PI / _itemCount * idx + _rotationAngle) * (radius - _rLength / 2.0);
            float y = center.y + sinf(2 * M_PI / _itemCount * idx + _rotationAngle) * (radius - _rLength / 2.0);
            
            attris.center = CGPointMake(x, y);
        }
        [_attributeAttay addObject:attris];
    }
}

// contentSize 的大小

- (CGSize)collectionViewContentSize{
    return self.collectionView.frame.size;
}

// cell / header / footer 的frame数组

- (NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect{
    return _attributeAttay;
}
```

2.由于需要监听滑动手势以调整布局，所以需要重载UICollectionVIew 事件方法

```objc
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{
    
    CGPoint centerPoint = self.center;
    UITouch *touch = touches.anyObject;
    CGPoint point  = [touch locationInView:self];
    
    CGFloat rLength = sqrt((point.x - centerPoint.x) * (point.x - centerPoint.x) + (point.y - centerPoint.y) * (point.y - centerPoint.y));
    
    // 手势范围限制
    
    if (!(rLength <= [self.largeRadius floatValue] && rLength >= [self.smallRadius floatValue])) {
        return;
    }
    
    [[NSNotificationCenter defaultCenter] postNotificationName:@"touchBegin" object:nil userInfo:@{@"x":[NSString stringWithFormat:@"%f",point.x],@"y":[NSString stringWithFormat:@"%f",point.y]}];
}

- (void)touchesMoved:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{
    
    CGPoint centerPoint = self.center;
    UITouch *touch = touches.anyObject;
    CGPoint point = [touch locationInView:self];
    
    CGFloat rLength = sqrt((point.x - centerPoint.x) * (point.x - centerPoint.x) + (point.y - centerPoint.y) * (point.y - centerPoint.y));
    
    // 手势范围限制
    
    if (!(rLength <= [self.largeRadius floatValue] && rLength >= [self.smallRadius floatValue])) {
        return;
    }
    
    [[NSNotificationCenter defaultCenter] postNotificationName:@"touchMoving" object:nil userInfo:@{@"x":[NSString stringWithFormat:@"%f",point.x],@"y":[NSString stringWithFormat:@"%f",point.y]}];
}

``` 

3.依据滑动前后手势的位置，计算滑动角度，重新计算每个item的坐标，然后重新布局

```objc
#pragma mark -- 按钮滑动，重新布局

// 滑动开始

- (void)touchBegin:(NSNotification *)sender{
    if (!_rotate) return;
    _centerPoint = self.collectionView.center;
    NSDictionary *dic = sender.userInfo;
    CGPoint point = CGPointMake([dic[@"x"] floatValue], [dic[@"y"] floatValue]);
    _lastPoint = point;
}

// 正在滑动中

- (void)touchMoving:(NSNotification *)sender{
    if (!_rotate) return;
    NSDictionary *dic = sender.userInfo;
    CGPoint point = CGPointMake([dic[@"x"] floatValue], [dic[@"y"] floatValue]);
    
    // 以collectionView center为中心计算滑动角度
    
    CGFloat rads = [self angleBetweenFirstLineStart:_centerPoint firstLineEnd:_lastPoint andSecondLineStart:_centerPoint secondLineEnd:point];
    
    if (_lastPoint.x != _centerPoint.x && point.x != _centerPoint.x) {
        
        CGFloat k1 = (_lastPoint.y - _centerPoint.y) / (_lastPoint.x - _centerPoint.x);
        CGFloat k2 = (point.y - _centerPoint.y) / (point.x - _centerPoint.x);
        if (k2 > k1) {
            _totalRads += rads;
        } else {
            _totalRads -= rads;
        }
    }
    
    _layout.rotationAngle = _totalRads;
    // 重新布局
    
    [_layout invalidateLayout];
    
    // 更新记录点
    
    _lastPoint = point;
}

// 两条直线之间的夹角

- (CGFloat)angleBetweenFirstLineStart:(CGPoint)firstLineStart firstLineEnd:(CGPoint)firstLineEnd andSecondLineStart:(CGPoint)secondLineStart secondLineEnd:(CGPoint)secondLineEnd{
    
    CGFloat a1 = firstLineEnd.x - firstLineStart.x;
    CGFloat b1 = firstLineEnd.y - firstLineStart.y;
    CGFloat a2 = secondLineEnd.x - secondLineStart.x;
    CGFloat b2 = secondLineEnd.y - secondLineStart.y;
    
    // 夹角余弦
    
    double cos = (a1 * a2 + b1 * b2) / (sqrt(pow(a1, 2) + pow(b1, 2)) * sqrt(pow(a2, 2) + pow(b2, 2)));
    
    // 浮点计算结果可能超过1，需要控制
    
    cos = cos > 1 ? 1 : cos;
    return acos(cos);
}
```
完整Demo地址：[YMLMenuDemo
](https://github.com/HuberyYang/YMLMenuDemo.git)















