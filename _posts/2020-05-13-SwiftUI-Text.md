--- 
layout:     post                      
title:      SwiftUI-Text
subtitle:   SwiftUI-Text
keywords:   SwiftUI, Text
date:       2020-05-13                
author:     HuberyYang                
header-img: img/article_head_road.jpg
catalog:    true                     
tags:
   - iOS                            
   - SwiftUI
   - Text
---


#### 字体

- 预设的字体

	```
	Text("SwiftUI is an innovative, exceptionally simple way to build user interfaces across all Apple platforms with the power of Swift. Build user interfaces for any Apple device using just one set of tools and APIs.")
		.font(.title)
	```
- 自定义字体

	```
	Text("SwiftUI is an innovative, exceptionally simple way to build user interfaces across all Apple platforms with the power of Swift. Build user interfaces for any Apple device using just one set of tools and APIs.")
		.font(Font.system(size: 15, weight: .bold, design: .default))
	```
	
#### 颜色

- 字体颜色

	```
	Text("SwiftUI is an innovative, exceptionally simple way to build user interfaces across all Apple platforms with the power of Swift. Build user interfaces for any Apple device using just one set of tools and APIs.")
		.foregroundColor(Color.black)
	```

- 背景色

	```
	Text("SwiftUI is an innovative, exceptionally simple way to build user interfaces across all Apple platforms with the power of Swift. Build user interfaces for any Apple device using just one set of tools and APIs.")
		.background(Color.red)
	```

#### 内容对齐方式

```
Text("SwiftUI is an innovative, exceptionally simple way to build user interfaces across all Apple platforms with the power of Swift. Build user interfaces for any Apple device using just one set of tools and APIs.")
	.multilineTextAlignment(.trailing)
```

#### 字体行数

> 如果需要自适应，不加这个设置就行，不能设为0，否则真就是0行了

```
Text("SwiftUI is an innovative, exceptionally simple way to build user interfaces across all Apple platforms with the power of Swift. Build user interfaces for any Apple device using just one set of tools and APIs.")
	.lineLimit(2)
```

#### 字体行间距

```
Text("SwiftUI is an innovative, exceptionally simple way to build user interfaces across all Apple platforms with the power of Swift. Build user interfaces for any Apple device using just one set of tools and APIs.")
	.lineSpacing(5)
```
