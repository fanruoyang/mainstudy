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
 - 修饰局部变量
    * 让局部变量只初始化一次
    * 局部变量在程序中只有一份内存
    * 并不会改变局部变量的作用域，仅仅是改变了局部变量的生命周期（只到程序结束，这个局部变量才会销毁）

 - 修饰全局变量
    * 全局变量的作用域仅限于当前文件

####7 extern 就是全局去找对应的变量

####8 copy的问题
###### 浅拷贝是对应的指针，深拷贝就是内存拷贝，产生新对象
    NSString *string = @"12";//不可变
    NSString *str1 = [string copy]; // 没有产生新对象-->浅靠谱
    NSMutableString *str2 = [string mutableCopy]; // 产生新对象-->深拷贝可变
    
    //可变的字符
     NSMutableString *string = [NSMutableString string];
    [string appendString:@"1"];
    [string appendString:@"2"];
    
    NSString *str1 = [string copy]; // 产生新对象-->深拷贝
    NSMutableString *str2 = [string mutableCopy]; // 产生新对象-->深拷贝
    
###### 对于类calss的拷贝，需要实现copy的协议
    @interface XMGPerson() <NSCopying>
    @end
    @implementation XMGPerson

    - (id)copyWithZone:(NSZone *)zone
    {
    XMGPerson *person = [[XMGPerson allocWithZone:zone] init];
    person.age = self.age;
    //    person.money = self.money;
    return person;
    }    
    
    --->文件实现方法
    void copyPerson()
    {
    XMGPerson *p = [[XMGPerson alloc] init];
    p.age = 10;
    p.money = 100.6;
    
    XMGPerson *p2 = [p copy]; // [p copyWithZone:zone]
    XMGPerson *p3 = [p copy];
    XMGPerson *p4 = [p copy];
    
    NSLog(@"%d %f", p2.age, p2.money);
    }
