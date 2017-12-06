

#### 使用NSOperation 基本
NSOperation本身是一个抽象类,要使用可以通过以下几个办法:
#####1 使用NSInvocationOperation
```
- (void)invocationOperation
{
    NSInvocationOperation *op = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(run) object:nil];
    [op start];
}
- (void)run
{
    NSLog(@"------%@", [NSThread currentThread]);
}
```
根据打印的结果我们会发现,直接调用start方法时,系统并不会开辟一个新的线程去执行任务,任务会在**当前线程同步执行**.

`注意:` 这里我们说的是当前线程而非主线程,意即:如果是在主线程中调用op的start方法,那么该任务是在主线程中执行;但如果是在其他子线程调用start方法,任务则是在其他子线程执行.

#####2 使用NSBlockOperation

```
- (void)blockOperation
{
    NSBlockOperation *op = [NSBlockOperation blockOperationWithBlock:^{
        // 在主线程
        NSLog(@"下载1------%@", [NSThread currentThread]);
    }];
    
    // 添加额外的任务(在子线程执行)
    [op addExecutionBlock:^{
        NSLog(@"下载2------%@", [NSThread currentThread]);
    }];

    [op addExecutionBlock:^{
        NSLog(@"下载3------%@", [NSThread currentThread]);
    }];
    [op addExecutionBlock:^{
        NSLog(@"下载4------%@", [NSThread currentThread]);
    }];
    [op start];
}
```
NSBlockOperation还有一种使用方法addExecutionBlock:使得我们可以给其添加更多的操作,**额外的任务在子线程执行**即**:当NSBlockOperation封装的操作数大于1的时候,就会执行异步操作.**

#####3 自定义NSOperation的子类

- 1.子类化NSOperation
- 2.在.m文件里面实现-(void)main方法
- 3.初始化该操作的时候直接调用alloc及init即可
- 4.同样可以通过start方法让你自定义的任务跑在当前线程中

##### 这里要注意 必须为自定义的 operation 提供 autorelease pool，因为 operation 完成后需要销毁。
```

   NSOperation *op=[[XMGOperation alloc] init];
- (void)main
{
    @autoreleasepool {
        for (NSInteger i = 0; i<1000; i++) {
            NSLog(@"download1 -%zd-- %@", i, [NSThread currentThread]);
        }
        if (self.isCancelled) return;
        
        for (NSInteger i = 0; i<1000; i++) {
            NSLog(@"download2 -%zd-- %@", i, [NSThread currentThread]);
        }
        if (self.isCancelled) return;
        
        for (NSInteger i = 0; i<1000; i++) {
            NSLog(@"download3 -%zd-- %@", i, [NSThread currentThread]);
        }
        if (self.isCancelled) return;
    }
}
```      
###### isCancelled 这个方法是在执行完这个一个任务时，移除剩下的任务，不这么写会不立即执行。
#### 如果是自定义并行的 NSOperation 则要复杂一点，首先必须重写以下几个方法：start: 所有并行的 Operations 都必须重写这个方法，然后在你想要执行的线程中手动调用这个方法。注意：任何时候都不能调用父类的start方法。
- 一般来说直接用队列进行并发操作，不进一步阐述
main: 在start方法中调用，但是注意要定义独立的自动释放池与别的线程区分开。
isExecuting: 是否执行中，需要实现KVO通知机制。
isFinished: 是否已完成，需要实现KVO通知机制。
isConcurrent: 该方法现在已经由isAsynchronous方法代替，并且 NSOperationQueue 也已经忽略这个方法的值。
isAsynchronous: 该方法默认返回 NO ，表示非并发执行。并发执行需要自定义并且返回 YES。后面会根据这个返回值来决定是否并发。

#### 怎样使用NSOperationQueue-->重要
##### 1 NSOperationQueue的队列类型
- 主队列
    - [NSOperationQueue mainQueue]
    - 凡是添加到主队列中的任务（NSOperation），都会放到主线程中执行
- 非主队列（其他队列）
    - [[NSOperationQueue alloc] init]
    - 同时包含了：串行、并发功能
    - 添加到这种队列中的任务（NSOperation），就会自动放到子线程中执行
    
##### 2 向NSOperationQueue中添加操作:
- 直接添加
     [queue addOperation:op1];
- 使用block添加,block的内容会被包装成operation对象添加到队列
    [queue addOperationWithBlock:^{ }];
    
    操作一但被到添加到队列中,就会自动异步执行.

##### 3 设置NSOperationQueue的最大并发数

NSOperationQueue可以通过以下方法设置最大并发数,
setMaxConcurrentOperationCount:,值得注意的是:**当并发数为1就变成了串行执行任务
**
```
  // 创建队列
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    
    // 设置最大并发操作数
    //    queue.maxConcurrentOperationCount = 2;
    queue.maxConcurrentOperationCount = 1; // 就变成了串行队列
    // 添加操作
    [queue addOperationWithBlock:^{
        NSLog(@"download1 --- %@", [NSThread currentThread]);
        [NSThread sleepForTimeInterval:0.01];
    }];
    [queue addOperationWithBlock:^{
        NSLog(@"download2 --- %@", [NSThread currentThread]);
        [NSThread sleepForTimeInterval:0.01];
    }];
```
##### 4 NSOperationQueue的暂停恢复和取消

- 取消
NSOperation有一个cancel方法可以取消单个操作
NSOperationQueue的cancelAllOperations相当于队列中的每个**operation调用了cancel方法,会取消队列里面全部的操作.**
但是,**不能取消正在进行中的任务**,队列调用了cancelAllOperations后会等当前正在进行的任务执行完闭后取消后面的操作
- 挂起和恢复
isSuspended : 判断是否挂起
setSuspended: YES表示挂起,NO表示恢复
和取消功能类似,**我们同样不能挂起正在运行中的操作**,队列会等当前操作结束后将后面的操作暂停(挂起)
##### 5 操作依赖

