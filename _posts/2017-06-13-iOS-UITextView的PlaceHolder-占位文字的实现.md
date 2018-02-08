UITextView的PlaceHolder 占位文字的实现方法网上已经有很多了，特别是 [VV木公](http://www.jianshu.com/u/93131dfba96a) 的 [史上最全的iOS之UITextView实现placeHolder占位文字的N种方法](http://www.jianshu.com/p/9edb8be75e0b) 文章里面总结的很全面，我只是对里面最后一种方法封装了一下，方便以后偷懒 ~ 嘻嘻

- .h 文件
```
    #import <UIKit/UIKit.h>  
      
    @interface UITextView (PlaceHolder)  
      
    /** 占位文字 */  
    @property (copy, nonatomic) NSString *placeHoldString;  
      
    /** 占位文字颜色 */  
    @property (strong, nonatomic) UIColor *placeHoldColor;  
      
    /** 占位文字字体 */  
    @property (strong, nonatomic) UIFont *placeHoldFont;  
      
    @end  
```

.m文件

```
#import "UITextView+PlaceHolder.h"  
#import <objc/runtime.h>  
  
static NSString *placeHoldLabelKey = @"placeHoldLabelKey";  
static NSString *placeHoldStringKey = @"placeHoldStringKey";  
static NSString *placeHoldColorKey = @"placeHoldColorKey";  
static NSString *placeHoldFontKey = @"placeHoldFontKey";  
  
@interface UITextView()  
  
@property (strong, nonatomic) UILabel *placeLabel;  
  
@end  
  
  
@implementation UITextView (PlaceHolder)  
  
- (void)setPlaceLabel:(UILabel *)placeLabel{  
    objc_setAssociatedObject(self, &placeHoldLabelKey, placeLabel, OBJC_ASSOCIATION_RETAIN);  
}  
  
- (UILabel *)placeLabel{  
    return objc_getAssociatedObject(self, &placeHoldLabelKey);  
}  
  
  
  
- (void)setPlaceHoldString:(NSString *)placeHoldString{  
      
    if (!self.placeLabel) {  
          
        self.placeLabel = [self setupCustomPlaceHoldLabel];  
    }  
      
    self.placeLabel.text = placeHoldString;  
    objc_setAssociatedObject(self, &placeHoldStringKey, placeHoldString, OBJC_ASSOCIATION_COPY);  
}  
  
- (NSString *)placeHoldString{  
    return objc_getAssociatedObject(self, &placeHoldStringKey);  
}  
  
  
- (void)setPlaceHoldColor:(UIColor *)placeHoldColor{  
      
    if (!self.placeLabel) {  
          
        self.placeLabel = [self setupCustomPlaceHoldLabel];  
    }  
      
    self.placeLabel.textColor = placeHoldColor;  
    objc_setAssociatedObject(self, &placeHoldColorKey, placeHoldColor, OBJC_ASSOCIATION_RETAIN);  
}  
  
- (UIColor *)placeHoldColor{  
    return objc_getAssociatedObject(self, &placeHoldColorKey);  
}  
  
  
  
- (void)setPlaceHoldFont:(UIFont *)placeHoldFont{  
      
    if (!self.placeLabel) {  
        self.placeLabel = [self setupCustomPlaceHoldLabel];  
    }  
      
    self.placeLabel.font = placeHoldFont;  
    objc_setAssociatedObject(self, &placeHoldFontKey, placeHoldFont, OBJC_ASSOCIATION_RETAIN);  
}  
  
- (UIFont *)placeHoldFont{  
    return objc_getAssociatedObject(self, &placeHoldFontKey);  
}  
  
  
- (UILabel *)setupCustomPlaceHoldLabel{  
  
    UILabel *label = [[UILabel alloc] init];  
    label.numberOfLines = 0;  
    label.textColor = [UIColor lightGrayColor];  
    label.font = self.font;  
    [label sizeToFit];  
    [self addSubview:label];  
    [self setValue:label forKey:@"_placeholderLabel"];  
      
    return label;  
}
```
