--- 
layout:     post                      
title:      iOS使用UICollectionView实现可旋转菜单
subtitle:   iOS使用UICollectionView实现可旋转菜单
date:       2017-01-01               
author:     HuberyYang                
header-img: img/post-bg-desk.jpg  
catalog:    true                     
tags:                             
    - iOS
    - OC
---

1.由于UICollectionVIew是依赖于UICollectionViewFlowLayout进行布局，而UICollectionViewFlowLayout 继承自 UICollectionViewLayout，所以如果我们想进行个性化布局，可以新建一个UICollectionViewLayout的子类来进行个性化布局。

```Objective-c
    //YMLRotationLayout.h 
 
    #import <UIKit/UIKit.h>  
      
    @interface YMLRotationLayout : UICollectionViewLayout  
      
    /** 
     按钮半径 
     */  
    @property (nonatomic,assign) CGFloat itemRadius;  
      
    /** 
     item相对圆心旋转角度 
     */  
    @property (nonatomic,assign) CGFloat rotationAngle;  
      
    @end  
```

```
    // YMLRotationLayout.m  
      
    #import "YMLRotationLayout.h"  
      
    @implementation YMLRotationLayout{  
          
        NSMutableArray * _attributeAttay;  
        CGFloat _rLength;  
        NSInteger _itemCount;  
    }  
      
    - (void)prepareLayout{  
        [super prepareLayout];  
          
        //获取item的个数  
        _itemCount = (int)[self.collectionView numberOfItemsInSection:0];  
        _attributeAttay = [[NSMutableArray alloc] init];  
        //先设定大圆的半径 取长和宽最短的  
        CGFloat radius = MIN(self.collectionView.frame.size.width, self.collectionView.frame.size.height)/2.2;  
        //计算圆心位置  
        CGPoint center = CGPointMake(self.collectionView.frame.size.width/2.0, self.collectionView.frame.size.height/2.0);  
          
        _rLength = _itemRadius;  
          
        //设置每个item的大小  
        for (int i = 0; i < _itemCount; i++) {  
              
            NSIndexPath *indexPath = [NSIndexPath indexPathForItem:i inSection:0];  
            UICollectionViewLayoutAttributes * attris = [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:indexPath];  
            //设置item大小  
            attris.size = CGSizeMake(_rLength, _rLength);  
            //计算每个item的圆心位置  
            /* 
             . 
             . . 
             .   . r 
             .     . 
             ......... 
             */  
            //计算每个item中心的坐标  
            //算出的x y值还要减去item自身的半径大小  
            float x = center.x + cosf(22 * M_PI / _itemCount * i + _rotationAngle) * (radius - _rLength / 2.0);  
            float y = center.y + sinf(22 * M_PI / _itemCount * i + _rotationAngle) * (radius - _rLength / 2.0);  
              
            attris.center = CGPointMake(x, y);  
            [_attributeAttay addObject:attris];  
        }  
    }  
      
    //设置内容区域的大小  
    - (CGSize)collectionViewContentSize{  
        return self.collectionView.frame.size;  
    }  
      
    //返回设置数组  
    - (NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect{  
        return _attributeAttay;  
    }  
```

2.由于需要监听滑动手势以调整布局，所以需要重载UICollectionVIew 事件方法

```
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{  
      
    CGPoint centerPoint = self.center;  
      
    UITouch *touch = touches.anyObject;  
    CGPoint point  = [touch locationInView:self];  
      
    CGFloat rLength = sqrt((point.x - centerPoint.x) * (point.x - centerPoint.x) + (point.y - centerPoint.y) * (point.y - centerPoint.y));  
    //设置触发手势范围  
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
    //设置触发手势范围  
    if (!(rLength <= [self.largeRadius floatValue] && rLength >= [self.smallRadius floatValue])) {  
        return;  
    }  
      
    [[NSNotificationCenter defaultCenter] postNotificationName:@"touchMoving" object:nil userInfo:@{@"x":[NSString stringWithFormat:@"%f",point.x],@"y":[NSString stringWithFormat:@"%f",point.y]}];  
}

``` 

3.上面两步完成之后，再依据滑动前后手势的位置，计算滑动角度，重新计算每个item的坐标，然后重新布局

```
#pragma mark -- 按钮滑动，重新布局  
  
//滑动开始  
- (void)touchBegin:(NSNotification *)sender{  
      
    _centerPoint = self.collectView.center;  
      
    NSDictionary *dic = sender.userInfo;  
    CGPoint point = CGPointMake([dic[@"x"] floatValue], [dic[@"y"] floatValue]);  
      
    _lastPoint = point;  
}  
  
//正在滑动中  
- (void)touchMoving:(NSNotification *)sender{  
      
    NSDictionary *dic = sender.userInfo;  
    CGPoint point = CGPointMake([dic[@"x"] floatValue], [dic[@"y"] floatValue]);  
      
    //手指当前所在的点与collectionView中心点x坐标差值  
    CGFloat x = point.x - _centerPoint.x;  
      
    //滑动中前后两点Y坐标差值  
    CGFloat difY = point.y - _lastPoint.y;  
      
    //以collectionView center为中心计算滑动角度  
    CGFloat rads = [self getAngleBetweenLinesWithline1Start:_centerPoint line1End:_lastPoint line2Start:_centerPoint line2End:point];  
      
    if (x >= 0) {  
          
        if (difY > 0) {  
              
            _totalRads += rads;  
              
        }else{  
              
            _totalRads -= rads;  
        }  
          
          
    } else {  
          
        if (difY > 0) {  
              
            _totalRads -= rads;  
        }else{  
              
            _totalRads += rads;  
        }  
          
    }  
      
    //将纯度数转化为π  
    _layout.rotationAngle = _totalRads / 180.0 * _rotationRate;  
      
    //collectionview 重新布局  
    [_layout invalidateLayout];  
      
    //更新上次的滑动点  
    _lastPoint = point;  
}  
  
//两条直线之间的夹角  
- (CGFloat)getAngleBetweenLinesWithline1Start:(CGPoint)line1Start line1End:(CGPoint)line1End line2Start:(CGPoint)line2Start line2End:(CGPoint)line2End{  
      
    CGFloat a = line1End.x - line1Start.x;  
    CGFloat b = line1End.y - line1Start.y;  
    CGFloat c = line2End.x - line2Start.x;  
    CGFloat d = line2End.y - line2Start.y;  
      
    CGFloat rads = acos(((a*c) + (b*d)) / ((sqrt(a*a + b*b)) * (sqrt(c*c + d*d))));  
      
    return radiansToDegrees(rads);  
      
}  
```
完整Demo地址：[https://github.com/HuberyYang/YMLMenuDemo.git](https://github.com/HuberyYang/YMLMenuDemo.git)















