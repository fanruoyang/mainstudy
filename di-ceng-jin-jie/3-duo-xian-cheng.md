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