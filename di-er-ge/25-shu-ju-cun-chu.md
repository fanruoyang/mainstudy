### 1.沙盒存储 pilst 归档----------  一般不用

- plist 存储一般来说都是读写俩个操作

- 写入

```
    // Plist注意：不能存储自定义对象
    // Plist：数组和字典,
    // 如何判断一个对象能不能使用Plist，就看下有没有writeToFile
    NSArray *arr = @[@"123",@1];  
    // 获取应用的文件夹（应用沙盒）
    // NSString *homePath = NSHomeDirectory(); 
    // 获取temp
    // NSTemporaryDirectory();  
    // 获取Cache文件路径
    // NSSearchPathDirectory:搜索的目录
    // NSSearchPathDomainMask：搜索范围 NSUserDomainMask:表示在用户的手机上查找
    // expandTilde 是否展开全路径，如果没有展开，应用的沙盒路径就是~
    // 存储一定要要展开路径
    NSString *cachePaht = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES)[0];
    // 拼接文件名
    NSString *filePath = [cachePaht stringByAppendingPathComponent:@"personArr.plist"];
    // File:文件的全路径
    [arr writeToFile:filePath atomically:YES];

```

- 读取
```
  NSString *cachePaht = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES)[0];
    // 拼接文件名
    NSString *filePath = [cachePaht stringByAppendingPathComponent:@"arr.plist"];
    
    NSArray *arr = [NSArray arrayWithContentsOfFile:filePath];
    
    NSLog(@"%@",arr);
```

### 2.偏好设置 Preference 保存———-- 一般用来保存账号密码和状态
>// 偏好设置存储:
// 好处：1.不需要关心文件名
// 2.快速做键值对存储
// 底层：就是封装了一个字典

- 存取
```
    NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];   
    [userDefaults setObject:@"xmg" forKey:@"account"];
    [userDefaults setObject:@"123" forKey:@"pwd"];
    [userDefaults setBool:YES forKey:@"rmbPwd"];
    // 在iOS7之前，默认不会马上把跟硬盘同步
    // 同步
    [userDefaults synchronize];
```
- 读取

      NSString *pwd = [[NSUserDefaults standardUserDefaults] objectForKey:@"pwd"];
       
### 3.归档 自定义对象存储__建立Model__存储读取
> 对应的Model 里面的方法

```
 1 .h 文件
    #import <Foundation/Foundation.h>
// 如果一个自定义对象想要归档，必须遵守NSCoding协议，实现协议方法。
@interface Person : NSObject<NSCoding>
@property (nonatomic, assign) int age;
@property (nonatomic, strong) NSString* name;
@end

2 .m 文件
// 什么时候调用：自定义对象归档的时候
// 作用：用来描述当前对象里面的哪些属性需要归档
- (void)encodeWithCoder:(NSCoder *)aCoder
{
    // name
    [aCoder encodeObject:_name forKey:@"name"];    
    // age
    [aCoder encodeInt:_age forKey:@"age"];
    
}
// 什么时候调用:解档对象的时候调用
// 作用：用来描述当前对象里面的哪些属性需要解档
// initWithCoder：就是用来解析文件的。
- (id)initWithCoder:(NSCoder *)aDecoder
{
    // super:NSObject
#warning 什么时候需要调用initWithCoder
    if (self = [super init]) {
        
        // 注意：一定要给成员变量赋值
        // name
       _name = [aDecoder decodeObjectForKey:@"name"];
        
        // age
       _age = [aDecoder decodeIntForKey:@"age"];

    }
    return self;  
}
```
- 存取
```
    Person *p = [[Person alloc] init];
    p.age = 18;
    
    // 获取cache
    NSString *cachePath = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES)[0];
    
    // 获取文件的全路径
    NSString *filePath = [cachePath stringByAppendingPathComponent:@"person.data"];
    
    // 把自定义对象归档
    [NSKeyedArchiver archiveRootObject:p toFile:filePath];
```
- 读取

```   
// 获取cache
    NSString *cachePath = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES)[0];
    
    // 获取文件的全路径
    NSString *filePath = [cachePath stringByAppendingPathComponent:@"person.data"];
    
    // 解档
    Person *p = [NSKeyedUnarchiver unarchiveObjectWithFile:filePath];
    
    NSLog(@"%d",p.age);
    
```