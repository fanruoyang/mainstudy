##### 1  pthread 了解
```objc
//执行的函数
void * run(void *param)
{
    for (NSInteger i = 0; i<50000; i++) {
        NSLog(@"------buttonClick---%zd--%@", i, [NSThread currentThread]);
    }
    return NULL;
}
- (IBAction)buttonClick:(id)sender {
    pthread_t thread;
    pthread_create(&thread, NULL, run, NULL);
    
    pthread_t thread2;
    pthread_create(&thread2, NULL, run, NULL);
}
```
##### 2 NSThread
```objc
// 隐式创建线程 自动启动--->子线程 后台线程
- (void)createThread3
{
    [self performSelectorInBackground:@selector(run:) withObject:@"jack"];
}
//简单设置线程 创建后自动启动
- (void)createThread2
{
    [NSThread detachNewThreadSelector:@selector(run:) toTarget:self withObject:@"rose"];
}

- (void)createThread1
{
        // 创建线程
        NSThread *thread = [[NSThread alloc] initWithTarget:self selector:@selector(run:) object:@"jack"];
        // 启动线程
        [thread start];

}
```
####### 优点是简单快捷  缺点是 无法对线程进行更详细的描述

##### 3 线程的状态
![](/assets/WX20171130-145754@2x.png)
```objc
//启动线程
- (void)start;
// 进入就绪状态 -> 运行状态。当线程任务执行完毕，自动进入死亡状态

//阻塞（暂停）线程
+ (void)sleepUntilDate:(NSDate *)date;
+ (void)sleepForTimeInterval:(NSTimeInterval)ti;
// 进入阻塞状态

//强制停止线程
+ (void)exit;
// 进入死亡状态

//注意：一旦线程停止（死亡）了，就不能再次开启任务
```
##### 4 线程安全
一块资源可能会被多个线程共享，也就是多个线程可能会访问同一块资源
比如多个线程访问同一个对象、同一个变量、同一个文件
当多个线程访问同一块资源时，很容易引发数据错乱和数据安全问题

##### 5 安全隐患解决  --互斥锁-->同步synchronized--
```
互斥锁使用格式
@synchronized(锁对象) { // 需要锁定的代码  }
    注意：锁定1份代码只用1把锁，用多把锁是无效的
    
    互斥锁的优缺点
    优点：能有效防止因多线程抢夺资源造成的数据安全问题
    缺点：需要消耗大量的CPU资源
    
    互斥锁的使用前提：多条线程抢夺同一块资源
    
    相关专业术语：线程同步-->
    线程同步的意思是：多条线程在同一条线上执行（按顺序地执行任务）
    互斥锁，就是使用了线程同步技术
```
##### 原子和非原子属性

###### atomic：原子属性，为setter方法加锁（默认就是atomic）
消耗内存，一般不用
###### nonatomic：非原子属性，不会为setter方法加锁

