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

####7 extern 就是全局去找对应的变量 const使用
    const 添加是不希望参数修改
    const 全局变量变为全局常量，可以在任意地方使用
    在加static 只能在本文件里面去使用

    int const a1;
    const int a2; 意义相同，不能修改
    ---如果常量是字符串 必须写在后面-->使无法在别处修改
    extern NSString *const FJdoorTypeRefreshNotification;

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
####  9 property 的修饰 copy ,strong 的区别
    copy 修饰，相当于set方法中将字符串copy为不可变对象，如果字符  串是可变的，依然变为不可变，无法修改
 
    strong 修饰，相当于 set 方法中 直接=赋值，还是原来的对象，如果字符串可变，依然可以进行修改
    
###### 如果用copy 修饰NSMutable不可变对象，本身写法是由问题的 

####  10 数组和指针的区别

      ## 指针p的加减法运算
- 指针p + N
    - p里面存储的地址值 + N * 指针所指向类型的占用字节数
- 指针p - N
    - p里面存储的地址值 - N * 指针所指向类型的占用字节数

######## 数组名
- 存储的是`数组首元素`的地址
- 等价于:一个指向`数组首元素`的指针
- `数组名 + 1` 的跨度：`数组首元素`的占用字节数

######## 其他结论
- `&num + 1`的跨度：`num`的占用字节数

       int numbers[4] = {10, 20, 30, 40};
        
        int *p = (int *)(&numbers + 1);
        
        NSLog(@"%d", *(p - 1));  // 结果40