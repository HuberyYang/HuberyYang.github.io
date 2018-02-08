1.如果只是设置选中状态的字体颜色，使用 tintColor  就可以达到效果
```
self.tabBar.tintColor = [UIColor redColor];
```
2.但如果要将未选中状态和选中状态下的颜色都改变，可以使用 ` setTitleTextAttributes:<#(nullable NSDictionary<NSString *,id> *)#> forState:<#(UIControlState)#> `达到效果
```
[[UITabBarItem appearance] setTitleTextAttributes:@{NSForegroundColorAttributeName:[UIColor redColor]} forState:UIControlStateNormal];  
[[UITabBarItem appearance] setTitleTextAttributes:@{NSForegroundColorAttributeName:[UIColor blueColor]} forState:UIControlStateSelected]; 
```
