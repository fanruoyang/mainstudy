####1 这里就是轮番图底层如何实现自动播放

####2 业务类的封装和提取

##### a.启动页的封装
##### b.存储
```
@implementation STSaveTool
+ (id)objectForKey:(NSString *)defaultName{
    return  [[NSUserDefaults standardUserDefaults] objectForKey:defaultName];
}

+ (void)setObject:(id)value forKey:(NSString *)defaultName
{
    [[NSUserDefaults standardUserDefaults] setObject:defaultName forKey:defaultName];
}
```
#### 3 高斯模糊效果
###### 截屏后进行处理 
#####a. DRNRealTimeBlurView 第三方框架，建立VIew 继承

#### 4 Block 的循环引用
###### 1. block会把代码里面的强指针全部强引用
###### 2. _weak typeof(self) weakSelf=self //typeof获取括号里面的类型

#### 5 同一个view添加到同一个父控件 添加多少次 都算一次，只有一份内存

#### 6 static