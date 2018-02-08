>编码的时候会时不时的遇到 nil、Nil、NULL、NSNull 中某一个，傻傻的分不清用法，现在我们来看看他们之间的区别

####1.nil 是 ObjC 对象的字面空值，对应 id 类型的对象，或者使用 @interface 声明的 ObjC 对象
```
NSString *someString = nil;  
NSURL *someURL = nil;  
id someObject = nil;  
if (anotherObject == nil) // do something
```
####2.Nil 是 ObjC 类类型的书面空值，对应 Class 类型对象
```
Class someClass = Nil;  
Class anotherClass = [NSString class];  
```
####3.NULL 是任意的 C 指针空值
```
intint *pointerToInt = NULL;  
charchar *pointerToChar = NULL;  
struct TreeNode *rootNode = NULL; 
```
####4.NSNull 是一个代表空值的类，是一个 ObjC 对象。实际上它只有一个单例方法：+[NSNull null]，一般用于表示集合中值为空的对象。
```
// 因为 nil 被用来用为集合结束的标志，所以 nil 不能存储在 Foundation 集合里。  
NSArray *array = [NSArray arrayWithObjects:@"one", @"two", nil nil];  
      
// 错误的使用  
NSMutableDictionary *dict = [NSMutableDictionary dictionary];  
[dict setObject:nil forKey:@"someKey"];  
      
// 正确的使用  
NSMutableDictionary *dict = [NSMutableDictionary dictionary];  
[dict setObject:[NSNull null] forKey:@"someKey"]; 
```
[转自 -- http://www.3lian.com/edu/2015/06-09/220517.html](http://www.3lian.com/edu/2015/06-09/220517.html)
