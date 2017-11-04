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
####### 截屏后进行处理 
#####a. DRNRealTimeBlurView 第三方框架，建立VIew 继承
