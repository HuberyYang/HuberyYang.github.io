--- 
layout:     post                      
title:      iOS 自定义设置UITabBar角标BadgeView的背景色
subtitle:   自定义设置UITabBar角标BadgeView的背景色
date:       2017-03-04                
author:     HuberyYang                
header-img: img/post-bg-desk.jpg  
catalog:    true                     
tags:                             
    - iOS
    - OC
---

UITabBar 角标 badgeView 的默认背景色是红色，有时候也需要根据需求进行修改，目前找到两种主要的处理的方式：

1.不使用原生的UITabBar，完全自定义控件

2.使用原生的UITabBar，但是运用UITabbar的扩展对badgeView自定义，处理方可以参考这篇文章：[http://www.jianshu.com/p/ccc0d24408fd](http://www.jianshu.com/p/ccc0d24408fd)

除了以上两种方式外，还有另外一种方式 ------ 对原生的UITabbar控件进行修改，这种方式也需要对 UITabBar 进行扩展，过程如下：

```
    #import "UITabBar+Custom.h"  
      
    @implementation UITabBar (Custom)  
      
    /** 
     *  该方法的调用一定要在设置了tabBarItem.badgeValue 之后使用 
     */  
    - (void)badgeViews:(void (^)(UIView *badgeView, UILabel *badgeLabel, UIView *badgeBackground))block {  
          
        if (block) {  
              
            for (UIView *tabBarButton in self.subviews)  
            {  
                /** 设置badgeView背景色就可设置角标的背景色 */  
                for (UIView *badgeView in tabBarButton.subviews)  
                {  
                    NSString *className = NSStringFromClass([badgeView class]);  
                      
                    if ([className rangeOfString:@"BadgeView"].location != NSNotFound)  
                    {  
                        /** badgeLabel 就是角标上数字显示框 */  
                        UILabel *badgeLabel;  
                          
                        /** badgeBackground 在badgeView上可能不存在，如果存在就可以进行自定义修改 */  
                        UIView *badgeBackground;  
                          
                        for (UIView *badgeSubview in badgeView.subviews)  
                        {  
                            NSString *className = NSStringFromClass([badgeSubview class]);  
                              
                            if ([badgeSubview isKindOfClass:[UILabel class]])  
                            {  
                                badgeLabel = (UILabel *)badgeSubview;  
                                  
                            } else if ([className rangeOfString:@"BadgeBackground"].location != NSNotFound) {  
                                  
                                badgeBackground = badgeSubview;  
                            }  
                        }  
                          
                        block(badgeView, badgeLabel, badgeBackground);  
                    }  
                }  
            }  
        }  
    }  
      
    @end  
```

使用场景：

```
[self.tabBar badgeViews:^(UIView *badgeView, UILabel *badgeLabel, UIView *badgeBackground) {  
      
    //这里进行相关自定义设置，如：背景色、显示内容，添加背景图片...  
    badgeView.backgroundColor = [UIColor redColor];  
      
}];
```
