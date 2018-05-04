#### 简单用法

```
-(void)demo1{
    //0 监听方法
    [[self.hkView rac_signalForSelector:@selector(btn:)] subscribeNext:^(RACTuple * _Nullable x) {
        NSLog(@"我监听到了你的消息%@",x);
    }];
    //代替KVO 1
    [self.hkView rac_observeKeyPath:@"frame" options:NSKeyValueObservingOptionNew observer:nil block:^(id value, NSDictionary *change, BOOL causedByDealloc, BOOL affectedOnlyLastComponent) {
        //
    }];
    //代替KVO 2
    [[self.hkView rac_valuesForKeyPath:@"frame" observer:self]subscribeNext:^(id  _Nullable x) {
        NSLog(@"%@",x);
    }];
    //3 监听通知的RAC
    [[[NSNotificationCenter defaultCenter] rac_addObserverForName:@"" object:nil]subscribeNext:^(NSNotification * _Nullable x) {
        NSLog(@"监听通知的RAC");
    }];
    //4 监听事件按钮
    
    [[_button rac_signalForControlEvents:UIControlEventTouchUpInside]subscribeNext:^(__kindof UIControl * _Nullable x) {
        NSLog(@"按钮点击事件%@",x);
    }];
    
    //5 监听文本框，文本完成
    [_textField.rac_textSignal subscribeNext:^(NSString * _Nullable x) {
        NSLog(@"123123%@",x);
    }];
    
    //6 RACtimer的一个延时响应---子线程中加载延时操作，循环请求 ----详细问题
    
    [[RACSignal interval:1.0 onScheduler:[RACScheduler scheduler]]subscribeNext:^(NSDate * _Nullable x) {
        NSLog(@"%@来了啊啊啊啊",[NSThread currentThread]);
    }];
    
    
    //7 监听文本框修改Label
    
    [_textField.rac_textSignal subscribeNext:^(NSString * _Nullable x) {
        _textLabel.text=x;
    }];
    //8 宏来写--用来给某个对象的某个属性，绑定的信号
    RAC(_textLabel,text)=_textField.rac_textSignal;
    
    [RACObserve(_textField, text)  subscribeNext:^(NSString * _Nullable x) {
        NSLog(@"123123%@",x);
    }];
    //循环引用 外面weak，里面strong ,解决循环引用，使用self。
    //    @weakify(self);
    //
    //    @strongify(self);
    //
    //    NSLog(@"%@",self);
    
    // 9.处理多个请求，都返回结果的时候，统一做处理.
    RACSignal *request1 = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {
        
        // 发送请求1
        [subscriber sendNext:@"发送请求1"];
        return nil;
    }];
    
    RACSignal *request2 = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {
        // 发送请求2
        [subscriber sendNext:@"发送请求2"];
        return nil;
    }];
    
    // 使用注意：几个信号，参数一的方法就几个参数，每个参数对应信号发出的数据。
    [self rac_liftSelector:@selector(updateUIWithR1:r2:) withSignalsFromArray:@[request1,request2]];
    
}
```