--- 
layout:     post                      
title:      SwiftUI-Image
subtitle:   SwiftUI-Image
keywords:   SwiftUI, Image
date:       2020-05-09                
author:     HuberyYang                
header-img: img/article_head_road.jpg
catalog:    true                     
tags:
   - iOS                            
   - SwiftUI
   - Image
---

### 加载本地图片

```
Image("local image name")
```

### 加载网络图片

- 使用官方api下载

	```
	import SwiftUI
	
	struct NewsItemRow: View {
	    
	    @State private var remoteImage: UIImage? = nil
	    let placehoulder = UIImage(named: "ic_placehoulder")!
	    
	    
	    var body: some View {
	        Image(uiImage: self.remoteImage ?? placehoulder)
	            .onAppear() {
	                self.fetchRemoteImage(urlString: "http://xxxxxx.jpg")
	        }
	    }
	    
	    func fetchRemoteImage(urlString: String) {
	        guard let url = URL(string: urlString) else { return }
	        URLSession.shared.dataTask(with: url){ (data, response, error) in
	            if let imageData = data, let image = UIImage(data: imageData){
	                self.remoteImage = image
	            } else{
	                kPrintLog(error)
	            }
	        }.resume()
	    }
	}
	```
- 使用 Kingfisher
	- Podfile 中 `platform` 需要iOS 13以上，如：`platform :ios, '13.0'`
	- 添加 pod `pod 'Kingfisher/SwiftUI'`
	- 执行 `pod install` 或者 `pod install --repo-update`
	- 在使用分地方导入 `import struct Kingfisher.KFImage`
	- 加载网络图片：

	```
	import SwiftUI
	import struct Kingfisher.KFImage
	
	struct NewsItemRow: View {
	    
	    var item: NewsItemModel
	    
	    var body: some View {
	        HStack {
	            KFImage(URL(string: item.pic ?? "")!)
	                .placeholder({ Image("ic_placehoulder") })
	                .resizable()
	                .scaledToFit()
	                .frame(width: 90, height: 90)
	        }
	    }
	}
	```
	
### 设置图片尺寸
> 注意：需要添加 `resizable()`

```
Image("local image name")
	.resizable()
	.frame(width: 90, height: 90)
```
### 设置图片缩放模式
> 有多种填充模式：`aspectRatio`、`aspectRatio`、`scaledToFit`、`scaledToFill`，按需选取

```
Image("local image name")
	.scaledToFit()
```
