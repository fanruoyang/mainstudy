##### 1 内存布局
![](/assets/63851550715425_.pic.jpg)
##### 2 retain
```
   //retain new alloc copy :引用计数加一
   //objc_retain -->哈希运算 找到 sidetale 散列表haxi --> refcont 将当前引用计数+1(偏移量)
```
##### 3 weak

```
  __weak   引用计数不会加一
    Runtime: waak(存储 self ---- waak 指针)
    哈希表: key --- 对象的地址  value ----- weak指针的地址
    *
    2 objc_initWeak 初始化一个weak指针指向某个对象的地址
    3 stroe_weak() 找到弱引用表的起始地址，注册weak-结构体--将weak对象存储到弱引用中
    4 在当前对象释放之后，遍历表 self ---> nil
    */
```
##### 4 NStimer

```
*
     * NStimer :如果管理生命周期
     self --- 消息的执行者,就是要强引用
     Nstimer  执行结束，delloc [timer invalidate]
     */
    
    //1 通过rulloop添加
    //2 利用消息转发
    
    _target = [NSObject new];
    class_addMethod([_target class],@selector(fire), class_getMethodImplementation([self class], @selector(fire)), "v@:");
    _timer = [NSTimer scheduledTimerWithTimeInterval:1.0f target:_target selector:@selector(fire) userInfo:nil repeats:YES];
    
    //2.1 多重代理实现
    
    _fyProxy = [FYProxy alloc];
    
    _fyProxy.target = self;
    _timer = [NSTimer scheduledTimerWithTimeInterval:1.0f target:_fyProxy selector:@selector(fire) userInfo:nil repeats:YES];
    
```

##### 5
    //3 UIviewConroller 的内存泄漏。 MLeakFineder  通过生命周期，父子关系找出有可能发生泄漏  willdealloc 方法交换发送消息