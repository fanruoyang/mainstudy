###1. GCD简介

什么是GCD呢？我们先来看看百度百科的解释简单了解下概念

引自百度百科
Grand Central Dispatch (GCD) 是Apple开发的一个多核编程的较新的解决方法。它主要用于优化应用程序以支持多核处理器以及其他对称多处理系统。它是一个在线程池模式的基础上执行的并行任务。在Mac OS X 10.6雪豹中首次推出，也可在IOS 4及以上版本使用。
为什么要用GCD呢？

因为GCD有很多好处啊，具体如下：

GCD可用于多核的并行运算
GCD会自动利用更多的CPU内核（比如双核、四核）
GCD会自动管理线程的生命周期（创建线程、调度任务、销毁线程）
程序员只需要告诉GCD想要执行什么任务，不需要编写任何线程管理代码
既然GCD有这么多的好处，那么下面我们就来系统的学习一下GCD的使用方法。

### 2. 任务和队列

学习GCD之前，先来了解GCD中两个核心概念：任务和队列。

任务：就是执行操作的意思，换句话说就是你在线程中执行的那段代码。在GCD中是放在block中的。执行任务有两种方式：同步执行和异步执行。两者的主要区别是：是否具备开启新线程的能力。

同步执行（sync）：只能在当前线程中执行任务，不具备开启新线程的能力
异步执行（async）：可以在新的线程中执行任务，具备开启新线程的能力
队列：这里的队列指任务队列，即用来存放任务的队列。队列是一种特殊的线性表，采用FIFO（先进先出）的原则，即新任务总是被插入到队列的末尾，而读取任务的时候总是从队列的头部开始读取。每读取一个任务，则从队列中释放一个任务。在GCD中有两种队列：串行队列和并行队列。

并行队列（Concurrent Dispatch Queue）：可以让多个任务并行（同时）执行（自动开启多个线程同时执行任务）
并行功能只有在异步（dispatch_async）函数下才有效
串行队列（Serial Dispatch Queue）：让任务一个接着一个地执行（一个任务执行完毕后，再执行下一个任务）
### 3. GCD的使用步骤

GCD的使用步骤其实很简单，只有两步。

创建一个队列（串行队列或并行队列）
将任务添加到队列中，然后系统就会根据任务类型执行任务（同步执行或异步执行）
下边来看看队列的创建方法和任务的创建方法。

#### 1. 队列的创建方法

可以使用dispatch_queue_create来创建对象，需要传入两个参数，第一个参数表示队列的唯一标识符，用于DEBUG，可为空；第二个参数用来识别是串行队列还是并行队列。DISPATCH_QUEUE_SERIAL表示串行队列，DISPATCH_QUEUE_CONCURRENT表示并行队列。
// 串行队列的创建方法
dispatch_queue_t queue= dispatch_queue_create("test.queue", DISPATCH_QUEUE_SERIAL);
// 并行队列的创建方法
dispatch_queue_t queue= dispatch_queue_create("test.queue", DISPATCH_QUEUE_CONCURRENT);
对于并行队列，还可以使用dispatch_get_global_queue来创建全局并行队列。GCD默认提供了全局的并行队列，需要传入两个参数。第一个参数表示队列优先级，一般用DISPATCH_QUEUE_PRIORITY_DEFAULT。第二个参数暂时没用，用0即可。
#### 2. 任务的创建方法

// 同步执行任务创建方法
dispatch_sync(queue, ^{
    NSLog(@"%@",[NSThread currentThread]);    // 这里放任务代码
});
// 异步执行任务创建方法
dispatch_async(queue, ^{
    NSLog(@"%@",[NSThread currentThread]);    // 这里放任务代码
});
虽然使用GCD只需两步，但是既然我们有两种队列，两种任务执行方式，那么我们就有了四种不同的组合方式。这四种不同的组合方式是

并行队列 + 同步执行
并行队列 + 异步执行
串行队列 + 同步执行
串行队列 + 异步执行
实际上，我们还有一种特殊队列是主队列，那样就有六种不同的组合方式了。

主队列 + 同步执行
主队列 + 异步执行
那么这几种不同组合方式各有什么区别呢，这里为了方便，先上结果，再来讲解。为图省事，直接查看表格结果，然后可以跳过** 4. GCD的基本使用** 了。
![](/assets/QQ20171201-095913@2x.png)


4. GCD的基本使用

先来讲讲并行队列的两种使用方法。

1. 并行队列 + 同步执行

不会开启新线程，执行完一个任务，再执行下一个任务
- (void) syncConcurrent
{
    NSLog(@"syncConcurrent---begin");
    
    dispatch_queue_t queue= dispatch_queue_create("test.queue", DISPATCH_QUEUE_CONCURRENT);
    
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"1------%@",[NSThread currentThread]);
        }
    });
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"2------%@",[NSThread currentThread]);
        }
    });
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"3------%@",[NSThread currentThread]);
        }
    });
    
    NSLog(@"syncConcurrent---end");
}
输出结果：
2016-09-03 19:22:27.577 GCD[11557:1897538] syncConcurrent---begin
2016-09-03 19:22:27.578 GCD[11557:1897538] 1------<NSThread: 0x7f82a1d058b0>{number = 1, name = main}
2016-09-03 19:22:27.578 GCD[11557:1897538] 1------<NSThread: 0x7f82a1d058b0>{number = 1, name = main}
2016-09-03 19:22:27.578 GCD[11557:1897538] 2------<NSThread: 0x7f82a1d058b0>{number = 1, name = main}
2016-09-03 19:22:27.579 GCD[11557:1897538] 2------<NSThread: 0x7f82a1d058b0>{number = 1, name = main}
2016-09-03 19:22:27.579 GCD[11557:1897538] 3------<NSThread: 0x7f82a1d058b0>{number = 1, name = main}
2016-09-03 19:22:27.579 GCD[11557:1897538] 3------<NSThread: 0x7f82a1d058b0>{number = 1, name = main}
2016-09-03 19:22:27.579 GCD[11557:1897538] syncConcurrent---end
从并行队列 + 同步执行中可以看到，所有任务都是在主线程中执行的。由于只有一个线程，所以任务只能一个一个执行。
同时我们还可以看到，所有任务都在打印的syncConcurrent---begin和syncConcurrent---end之间，这说明任务是添加到队列中马上执行的。
2. 并行队列 + 异步执行

可同时开启多线程，任务交替执行
- (void) asyncConcurrent
{
    NSLog(@"asyncConcurrent---begin");
    
    dispatch_queue_t queue= dispatch_queue_create("test.queue", DISPATCH_QUEUE_CONCURRENT);
    
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"1------%@",[NSThread currentThread]);
        }
    });
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"2------%@",[NSThread currentThread]);
        }
    });
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"3------%@",[NSThread currentThread]);
        }
    });
    
    NSLog(@"asyncConcurrent---end");
}

输出结果：
2016-09-03 19:27:31.503 GCD[11595:1901548] asyncConcurrent---begin
2016-09-03 19:27:31.504 GCD[11595:1901548] asyncConcurrent---end
2016-09-03 19:27:31.504 GCD[11595:1901626] 1------<NSThread: 0x7f8309c22080>{number = 2, name = (null)}
2016-09-03 19:27:31.504 GCD[11595:1901625] 2------<NSThread: 0x7f8309f0b790>{number = 4, name = (null)}
2016-09-03 19:27:31.504 GCD[11595:1901855] 3------<NSThread: 0x7f8309e1a950>{number = 3, name = (null)}
2016-09-03 19:27:31.504 GCD[11595:1901626] 1------<NSThread: 0x7f8309c22080>{number = 2, name = (null)}
2016-09-03 19:27:31.504 GCD[11595:1901625] 2------<NSThread: 0x7f8309f0b790>{number = 4, name = (null)}
2016-09-03 19:27:31.505 GCD[11595:1901855] 3------<NSThread: 0x7f8309e1a950>{number = 3, name = (null)}
在并行队列 + 异步执行中可以看出，除了主线程，又开启了3个线程，并且任务是交替着同时执行的。
另一方面可以看出，所有任务是在打印的syncConcurrent---begin和syncConcurrent---end之后才开始执行的。说明任务不是马上执行，而是将所有任务添加到队列之后才开始异步执行。
接下来再来讲讲串行队列的执行方法。

3. 串行队列 + 同步执行

不会开启新线程，在当前线程执行任务。任务是串行的，执行完一个任务，再执行下一个任务
- (void) syncSerial
{
    NSLog(@"syncSerial---begin");
    
    dispatch_queue_t queue = dispatch_queue_create("test.queue", DISPATCH_QUEUE_SERIAL);
    
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"1------%@",[NSThread currentThread]);
        }
    });
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"2------%@",[NSThread currentThread]);
        }
    });
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"3------%@",[NSThread currentThread]);
        }
    });
    
    NSLog(@"syncSerial---end");
}
输出结果为：
2016-09-03 19:29:00.066 GCD[11622:1903904] syncSerial---begin
2016-09-03 19:29:00.067 GCD[11622:1903904] 1------<NSThread: 0x7fa2e9f00980>{number = 1, name = main}
2016-09-03 19:29:00.067 GCD[11622:1903904] 1------<NSThread: 0x7fa2e9f00980>{number = 1, name = main}
2016-09-03 19:29:00.067 GCD[11622:1903904] 2------<NSThread: 0x7fa2e9f00980>{number = 1, name = main}
2016-09-03 19:29:00.067 GCD[11622:1903904] 2------<NSThread: 0x7fa2e9f00980>{number = 1, name = main}
2016-09-03 19:29:00.067 GCD[11622:1903904] 3------<NSThread: 0x7fa2e9f00980>{number = 1, name = main}
2016-09-03 19:29:00.068 GCD[11622:1903904] 3------<NSThread: 0x7fa2e9f00980>{number = 1, name = main}
2016-09-03 19:29:00.068 GCD[11622:1903904] syncSerial---end
在串行队列 + 同步执行可以看到，所有任务都是在主线程中执行的，并没有开启新的线程。而且由于串行队列，所以按顺序一个一个执行。
同时我们还可以看到，所有任务都在打印的syncConcurrent---begin和syncConcurrent---end之间，这说明任务是添加到队列中马上执行的。
4. 串行队列 + 异步执行

会开启新线程，但是因为任务是串行的，执行完一个任务，再执行下一个任务
- (void) asyncSerial
{
    NSLog(@"asyncSerial---begin");
    
    dispatch_queue_t queue = dispatch_queue_create("test.queue", DISPATCH_QUEUE_SERIAL);
    
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"1------%@",[NSThread currentThread]);
        }
    });
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"2------%@",[NSThread currentThread]);
        }
    });
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"3------%@",[NSThread currentThread]);
        }
    });
    
    NSLog(@"asyncSerial---end");
}
输出结果为：
2016-09-03 19:30:08.363 GCD[11648:1905817] asyncSerial---begin
2016-09-03 19:30:08.364 GCD[11648:1905817] asyncSerial---end
2016-09-03 19:30:08.364 GCD[11648:1905895] 1------<NSThread: 0x7fb548c0e390>{number = 2, name = (null)}
2016-09-03 19:30:08.364 GCD[11648:1905895] 1------<NSThread: 0x7fb548c0e390>{number = 2, name = (null)}
2016-09-03 19:30:08.364 GCD[11648:1905895] 2------<NSThread: 0x7fb548c0e390>{number = 2, name = (null)}
2016-09-03 19:30:08.364 GCD[11648:1905895] 2------<NSThread: 0x7fb548c0e390>{number = 2, name = (null)}
2016-09-03 19:30:08.365 GCD[11648:1905895] 3------<NSThread: 0x7fb548c0e390>{number = 2, name = (null)}
2016-09-03 19:30:08.365 GCD[11648:1905895] 3------<NSThread: 0x7fb548c0e390>{number = 2, name = (null)}
在串行队列 + 异步执行可以看到，开启了一条新线程，但是任务还是串行，所以任务是一个一个执行。
另一方面可以看出，所有任务是在打印的syncConcurrent---begin和syncConcurrent---end之后才开始执行的。说明任务不是马上执行，而是将所有任务添加到队列之后才开始同步执行。
下边讲讲刚才我们提到过的特殊队列——主队列。

主队列：GCD自带的一种特殊的串行队列
所有放在主队列中的任务，都会放到主线程中执行
可使用dispatch_get_main_queue()获得主队列
我们再来看看主队列的两种组合方式。

5. 主队列 + 同步执行

互等卡住不可行(在主线程中调用)
- (void)syncMain
{
    NSLog(@"syncMain---begin");
    
    dispatch_queue_t queue = dispatch_get_main_queue();
    
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"1------%@",[NSThread currentThread]);
        }
    });
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"2------%@",[NSThread currentThread]);
        }
    });
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"3------%@",[NSThread currentThread]);
        }
    });
    
    NSLog(@"syncMain---end");
}

输出结果
2016-09-03 19:32:15.356 GCD[11670:1908306] syncMain---begin
这时候，我们惊奇的发现，在主线程中使用主队列 + 同步执行，任务不再执行了，而且syncMain---end也没有打印。这是为什么呢？

这是因为我们在主线程中执行这段代码。我们把任务放到了主队列中，也就是放到了主线程的队列中。而同步执行有个特点，就是对于任务是立马执行的。那么当我们把第一个任务放进主队列中，它就会立马执行。但是主线程现在正在处理syncMain方法，所以任务需要等syncMain执行完才能执行。而syncMain执行到第一个任务的时候，又要等第一个任务执行完才能往下执行第二个和第三个任务。

那么，现在的情况就是syncMain方法和第一个任务都在等对方执行完毕。这样大家互相等待，所以就卡住了，所以我们的任务执行不了，而且syncMain---end也没有打印。

要是如果不再主线程中调用，而在其他线程中调用会如何呢？

不会开启新线程，执行完一个任务，再执行下一个任务（在其他线程中调用）
dispatch_queue_t queue = dispatch_queue_create("test.queue", DISPATCH_QUEUE_CONCURRENT);

dispatch_async(queue, ^{
    [self syncMain];
});
输出结果：
2016-09-03 19:32:45.496 GCD[11686:1909617] syncMain---begin
2016-09-03 19:32:45.497 GCD[11686:1909374] 1------<NSThread: 0x7faef2f01600>{number = 1, name = main}
2016-09-03 19:32:45.498 GCD[11686:1909374] 1------<NSThread: 0x7faef2f01600>{number = 1, name = main}
2016-09-03 19:32:45.498 GCD[11686:1909374] 2------<NSThread: 0x7faef2f01600>{number = 1, name = main}
2016-09-03 19:32:45.498 GCD[11686:1909374] 2------<NSThread: 0x7faef2f01600>{number = 1, name = main}
2016-09-03 19:32:45.499 GCD[11686:1909374] 3------<NSThread: 0x7faef2f01600>{number = 1, name = main}
2016-09-03 19:32:45.499 GCD[11686:1909374] 3------<NSThread: 0x7faef2f01600>{number = 1, name = main}
2016-09-03 19:32:45.499 GCD[11686:1909617] syncMain---end
在其他线程中使用主队列 + 同步执行可看到：所有任务都是在主线程中执行的，并没有开启新的线程。而且由于主队列是串行队列，所以按顺序一个一个执行。
同时我们还可以看到，所有任务都在打印的syncConcurrent---begin和syncConcurrent---end之间，这说明任务是添加到队列中马上执行的。
6. 主队列 + 异步执行

只在主线程中执行任务，执行完一个任务，再执行下一个任务
- (void)asyncMain
{
    NSLog(@"asyncMain---begin");
    
    dispatch_queue_t queue = dispatch_get_main_queue();
    
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"1------%@",[NSThread currentThread]);
        }
    });
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"2------%@",[NSThread currentThread]);
        }
    });
    dispatch_async(queue, ^{
        for (int i = 0; i < 2; ++i) {
            NSLog(@"3------%@",[NSThread currentThread]);
        }
    });
    
    NSLog(@"asyncMain---end");
}
输出结果：
2016-09-03 19:33:54.995 GCD[11706:1911313] asyncMain---begin
2016-09-03 19:33:54.996 GCD[11706:1911313] asyncMain---end
2016-09-03 19:33:54.996 GCD[11706:1911313] 1------<NSThread: 0x7fb623d015e0>{number = 1, name = main}
2016-09-03 19:33:54.997 GCD[11706:1911313] 1------<NSThread: 0x7fb623d015e0>{number = 1, name = main}
2016-09-03 19:33:54.997 GCD[11706:1911313] 2------<NSThread: 0x7fb623d015e0>{number = 1, name = main}
2016-09-03 19:33:54.997 GCD[11706:1911313] 2------<NSThread: 0x7fb623d015e0>{number = 1, name = main}
2016-09-03 19:33:54.997 GCD[11706:1911313] 3------<NSThread: 0x7fb623d015e0>{number = 1, name = main}
2016-09-03 19:33:54.997 GCD[11706:1911313] 3------<NSThread: 0x7fb623d015e0>{number = 1, name = main}
我们发现所有任务都在主线程中，虽然是异步执行，具备开启线程的能力，但因为是主队列，所以所有任务都在主线程中，并且一个接一个执行。
另一方面可以看出，所有任务是在打印的syncConcurrent---begin和syncConcurrent---end之后才开始执行的。说明任务不是马上执行，而是将所有任务添加到队列之后才开始同步执行。
弄懂了难理解、绕来绕去的队列+任务之后，我们来学习一个简单的东西——GCD线程之间的通讯。

5. GCD线程之间的通讯

在iOS开发过程中，我们一般在主线程里边进行UI刷新，例如：点击、滚动、拖拽等事件。我们通常把一些耗时的操作放在其他线程，比如说图片下载、文件上传等耗时操作。而当我们有时候在其他线程完成了耗时操作时，需要回到主线程，那么就用到了线程之间的通讯。

dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    for (int i = 0; i < 2; ++i) {
        NSLog(@"1------%@",[NSThread currentThread]);
    }
    
    // 回到主线程
    dispatch_async(dispatch_get_main_queue(), ^{
        NSLog(@"2-------%@",[NSThread currentThread]);
    });
});
输出结果：
2016-09-03 19:34:59.165 GCD[11728:1913039] 1------<NSThread: 0x7f8319c06820>{number = 2, name = (null)}
2016-09-03 19:34:59.166 GCD[11728:1913039] 1------<NSThread: 0x7f8319c06820>{number = 2, name = (null)}
2016-09-03 19:34:59.166 GCD[11728:1912961] 2-------<NSThread: 0x7f8319e00560>{number = 1, name = main}
可以看到在其他线程中先执行操作，执行完了之后回到主线程执行主线程的相应操作。
6. GCD的其他方法

1. GCD的栅栏方法 dispatch_barrier_async

我们有时需要异步执行两组操作，而且第一组操作执行完之后，才能开始执行第二组操作。这样我们就需要一个相当于栅栏一样的一个方法将两组异步执行的操作组给分割起来，当然这里的操作组里可以包含一个或多个任务。这就需要用到dispatch_barrier_async方法在两个操作组间形成栅栏。

- (void)barrier
{
    dispatch_queue_t queue = dispatch_queue_create("12312312", DISPATCH_QUEUE_CONCURRENT);
    
    dispatch_async(queue, ^{
        NSLog(@"----1-----%@", [NSThread currentThread]);
    });
    dispatch_async(queue, ^{
        NSLog(@"----2-----%@", [NSThread currentThread]);
    });
    
    dispatch_barrier_async(queue, ^{
        NSLog(@"----barrier-----%@", [NSThread currentThread]);
    });
    
    dispatch_async(queue, ^{
        NSLog(@"----3-----%@", [NSThread currentThread]);
    });
    dispatch_async(queue, ^{
        NSLog(@"----4-----%@", [NSThread currentThread]);
    });
}
输出结果：
2016-09-03 19:35:51.271 GCD[11750:1914724] ----1-----<NSThread: 0x7fb1826047b0>{number = 2, name = (null)}
2016-09-03 19:35:51.272 GCD[11750:1914722] ----2-----<NSThread: 0x7fb182423fd0>{number = 3, name = (null)}
2016-09-03 19:35:51.272 GCD[11750:1914722] ----barrier-----<NSThread: 0x7fb182423fd0>{number = 3, name = (null)}
2016-09-03 19:35:51.273 GCD[11750:1914722] ----3-----<NSThread: 0x7fb182423fd0>{number = 3, name = (null)}
2016-09-03 19:35:51.273 GCD[11750:1914724] ----4-----<NSThread: 0x7fb1826047b0>{number = 2, name = (null)}
可以看出在执行完栅栏前面的操作之后，才执行栅栏操作，最后再执行栅栏后边的操作。
2. GCD的延时执行方法 dispatch_after

当我们需要延迟执行一段代码时，就需要用到GCD的dispatch_after方法。

dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
    // 2秒后异步执行这里的代码...
    NSLog(@"run-----");
});
3. GCD的一次性代码(只执行一次) dispatch_once

我们在创建单例、或者有整个程序运行过程中只执行一次的代码时，我们就用到了GCD的dispatch_once方法。使用dispatch_once函数能保证某段代码在程序运行过程中只被执行1次。

static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
    // 只执行1次的代码(这里面默认是线程安全的)
});
4. GCD的快速迭代方法 dispatch_apply

通常我们会用for循环遍历，但是GCD给我们提供了快速迭代的方法dispatch_apply，使我们可以同时遍历。比如说遍历0~5这6个数字，for循环的做法是每次取出一个元素，逐个遍历。dispatch_apply可以同时遍历多个数字。
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

dispatch_apply(6, queue, ^(size_t index) {
    NSLog(@"%zd------%@",index, [NSThread currentThread]);
});
输出结果：
2016-09-03 19:37:02.250 GCD[11764:1915764] 1------<NSThread: 0x7fac9a7029e0>{number = 1, name = main}
2016-09-03 19:37:02.250 GCD[11764:1915885] 0------<NSThread: 0x7fac9a614bd0>{number = 2, name = (null)}
2016-09-03 19:37:02.250 GCD[11764:1915886] 2------<NSThread: 0x7fac9a542b20>{number = 3, name = (null)}
2016-09-03 19:37:02.251 GCD[11764:1915764] 4------<NSThread: 0x7fac9a7029e0>{number = 1, name = main}
2016-09-03 19:37:02.250 GCD[11764:1915884] 3------<NSThread: 0x7fac9a76ca10>{number = 4, name = (null)}
2016-09-03 19:37:02.251 GCD[11764:1915885] 5------<NSThread: 0x7fac9a614bd0>{number = 2, name = (null)}
从输出结果中前边的时间中可以看出，几乎是同时遍历的。

5. GCD的队列组 dispatch_group

有时候我们会有这样的需求：分别异步执行2个耗时操作，然后当2个耗时操作都执行完毕后再回到主线程执行操作。这时候我们可以用到GCD的队列组。
我们可以先把任务放到队列中，然后将队列放入队列组中。
调用队列组的dispatch_group_notify回到主线程执行操作。

dispatch_group_t group =  dispatch_group_create();

dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    // 执行1个耗时的异步操作
});

dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    // 执行1个耗时的异步操作
});

dispatch_group_notify(group, dispatch_get_main_queue(), ^{
    // 等前面的异步操作都执行完毕后，回到主线程...
});

