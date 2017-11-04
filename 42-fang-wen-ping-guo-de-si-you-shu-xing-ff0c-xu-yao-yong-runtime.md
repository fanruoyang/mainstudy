#### 1 runtime  获取系统手势

```objc
    self.interactivePopGestureRecognizer.enabled = NO;
    
    // 取出系统手势的target对象，
    
    //  取出某个对象里面属性 1.KVC 前提条件：必须知道属性名 2.运行时
    // 遍历某个类里面所有属性 Ivar:表示成员属性
    // copyIvarList只能获取哪个类下面的属性，并不会越界（不会把它的父类的属性给遍历出来）
    // Class 获取哪个类的成员属性
    // count:告诉你当前类里面成员属性的总数
        unsigned int count = 0;
        // 返回成员属性的数组
        Ivar *ivars = class_copyIvarList([UIGestureRecognizer class], &count);
    
        for (int i = 0; i < count; i++) {
            // 取出成员变量
            Ivar ivar = ivars[i];
            // 获取属性名
            NSString *ivarName = @(ivar_getName(ivar)); 
            NSLog(@"%@",ivarName);
        }
    // _targets:属性名 value
    NSArray *targets = [self.interactivePopGestureRecognizer valueForKeyPath:@"_targets"];
    id objc = [targets firstObject];  
    id target = [objc valueForKeyPath:@"_target"];
```

#### 2 侧滑方法 就是利用系统的手势将范围变大，系统本身是从边界开始

##### 注意的情况是 target 实际就是系统手势的代理方法
######self.interactivePopGestureRecognizer.delegate==target
```
    
    UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self.interactivePopGestureRecognizer.delegate action:@selector(handleNavigationTransition:)];
    
    pan.delegate = self;
    
    [self.view addGestureRecognizer:pan];
``` 
#### 3 代码
```objc
- (void)addGestureRecognizer {
    UIGestureRecognizer *gesture = self.interactivePopGestureRecognizer;
    gesture.enabled = NO;
    UIView *gestureView = gesture.view;
    UIPanGestureRecognizer *popRecognizer = [[UIPanGestureRecognizer alloc] init];
    popRecognizer.delegate = self;
    popRecognizer.maximumNumberOfTouches = 1;
    [gestureView addGestureRecognizer:popRecognizer];
    
    //获取系统手势的target数组
    NSMutableArray *_targets = [gesture valueForKey:@"_targets"];
    //获取它的唯一对象，我们知道它是一个叫UIGestureRecognizerTarget的私有类，它有一个属性叫_target
    id gestureRecognizerTarget = [_targets firstObject];
    //获取_target:_UINavigationInteractiveTransition，它有一个方法叫handleNavigationTransition:
    id navigationInteractiveTransition = [gestureRecognizerTarget valueForKey:@"_target"];
    //通过前面的打印，我们从控制台获取出来它的方法签名。
    SEL handleTransition = NSSelectorFromString(@"handleNavigationTransition:");
    //创建一个与系统一模一样的手势，我们只把它的类改为UIPanGestureRecognizer
    [popRecognizer addTarget:navigationInteractiveTransition action:handleTransition];
}

- (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer {
    /**
     *  这里有两个条件不允许手势执行，1、当前控制器为根控制器；2、如果这个push、pop动画正在执行（私有属性）
     */
    return self.viewControllers.count != 1 && ![[self valueForKey:@"_isTransitioning"] boolValue];
}

- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch {
    CGPoint touchLocation = [touch locationInView:touch.view];
    
    CGFloat absX = fabs(touchLocation.x);
    CGFloat absY = fabs(touchLocation.y);
    
    // 设置滑动有效距离
    if (MAX(absX, absY) < 10)
        return NO;
    
    if (absX > absY ) {
        
        if (touchLocation.x<0) {
            
            //向左滑动
            return YES;
        }else{
            //向右滑动
            return NO;
        }
        
    } else if (absY > absX) {
        if (touchLocation.y<0) {
            
            //向上滑动
            return YES;
        }else{
            
            //向下滑动
            return YES;
        }
    }

    return  YES;
}



```
