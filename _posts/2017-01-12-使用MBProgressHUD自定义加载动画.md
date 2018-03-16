--- 
layout:     post                      
title:      MBProgressHUD自定义加载动画
subtitle:   MBProgressHUD自定义加载动画
keywords:   ios,MBProgressHUD,自定义加载动画
date:       2017-01-12               
author:     HuberyYang                
header-img: img/post-bg-desk.jpg  
catalog:    true                     
tags:                             
    - iOS
    - OC
---

MBProgressHUD除了内置的加载效果外，还支持用户自定义加载效果。我们可以使用自定义imageView 加载gif 图片实现动画效果，过程如下：

```
    + (void)showGifToView:(UIView *)view{  
          
        MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:view animated:YES];  
          
        //使用SDWebImage 放入gif 图片  
        UIImage *image = [UIImage sd_animatedGIFNamed:@"load_antion"];  
          
        //自定义imageView  
        UIImageView *cusImageV = [[UIImageView alloc] initWithImage:image];  
          
        //设置hud模式  
        hud.mode = MBProgressHUDModeCustomView;  
          
        //设置在hud影藏时将其从SuperView上移除,自定义情况下默认为NO  
        hud.removeFromSuperViewOnHide = YES;  
          
        //设置方框view为该模式后修改颜色才有效果  
          
        hud.bezelView.style = MBProgressHUDBackgroundStyleSolidColor;  
          
        //设置方框view背景色  
        hud.bezelView.backgroundColor = [UIColor clearColor];  
          
        //设置总背景view的背景色，并带有透明效果  
        hud.backgroundColor = [[UIColor blackColor] colorWithAlphaComponent:0.3]; hud.customView = cusImageV;  
    }  
```
