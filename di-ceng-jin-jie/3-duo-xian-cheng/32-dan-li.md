##### 1 作用
可以保证在程序运行过程，一个类只有一个实例，而且该实例易于供外界访问
从而方便地控制了实例个数，并节约系统资源
###### 只有一份内存
##### 2 使用场合
在整个应用程序中，共享一份资源（这份资源只需要创建初始化1次），一般用于工具类。例如：登陆控制器，网络数据请求，音乐播放器等一个工程需要使用多次的控制器或方法。
##### 3 单例模式的优缺点
优点：
单例模式可以保证系统中一个类只有一个实例而且该实例易于外界访问，从而方便对实例个数的控制并节约系统资源。
如果希望在系统中某个类的对象只能存在一个，单例模式是最好的解决方案。
单例模式因为类控制了实例化过程，所以类可以更加灵活修改实例化过程。
缺点：
单例对象一旦建立，对象指针是保存在静态区的，单例对象在堆中分配的内存空间，会在应用程序终止后才会被释放。
单例类无法继承，因此很难进行类的扩展。
单例不适用于变化的对象，如果同一类型的对象总是要在不同的用例场景发生变化，单例就会引起数据的错误，不能保存彼此的状态。

注意：我们在使用单例类之前，一定要考虑好单例类是否适合和类以后的扩展性，避免盲目滥用单例

##### 4 ARC中单例实现步骤
- 1 在类的内部提供一个static修饰的全局变量 
- 2 提供一个类方法，方便外界访问
######- 3 重写+allocWithZone方法，保证永远都只为单例对象分配一次内存空间
- 4 严谨起见，重写-copyWithZone方法和-MutableCopyWithZone方法

##### 5 ARC中单例代码实现
```
#import "Tools.h"
@implementation Tools
// 创建静态对象 防止外部访问
static Tools *_instance;
+(instancetype)allocWithZone:(struct _NSZone *)zone
{
//    @synchronized (self) {
//        // 为了防止多线程同时访问对象，造成多次分配内存空间，所以要加上线程锁
//        if (_instance == nil) {
//            _instance = [super allocWithZone:zone];
//        }
//        return _instance;
//    }
    // 也可以使用一次性代码
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        if (_instance == nil) {
            _instance = [super allocWithZone:zone];
        }
    });
    return _instance;
}
// 为了使实例易于外界访问 我们一般提供一个类方法
// 类方法命名规范 share类名|default类名|类名
+(instancetype)shareTools
{
    //return _instance;
    // 最好用self 用Tools他的子类调用时会出现错误
    return [[self alloc]init];
}
// 为了严谨，也要重写copyWithZone 和 mutableCopyWithZone
-(id)copyWithZone:(NSZone *)zone
{
    return _instance;
}
-(id)mutableCopyWithZone:(NSZone *)zone
{
    return _instance;
}

```
##### 可以在.h 文件里面定义一个宏 ，然后调用.h文件就可以用了

##### \在宏定义里面表示包含后面的一行
#####  ##在宏定义里面 是连接符，可以替换前面相同的参数