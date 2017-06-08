###1.UIRsponder 

- UIResponder内部提供了以下方法来处理事件
  - 触摸事件
 
    ```
    - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;
    - (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;
    - (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event;
    - (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;

    ```
 - 加速器事件
    ```
    - (void)motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event;
    - (void)motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event;
    - (void)motionCancelled:(UIEventSubtype)motion withEvent:(UIEvent *)event;
    ```
 - 远程控制事件
 
      -(void)remoteControlReceivedWithEvent:(UIEvent *)event;
      
#### 1.UIView的点击拖拽事件

   ```
     // 当手指开始触摸view
// NSArray,字典，NSSet(无序)
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    NSLog(@"%ld", touches.count);
    NSLog(@"%s",__func__);
}

// 当手指在view上移动的时候
- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event
{
    NSLog(@"%s",__func__);
    
    // 获取UITouch对象
    UITouch *touch = [touches anyObject];
    
    // 获取当前点
    CGPoint curP = [touch locationInView:self];
    
    // 获取上一个点
    CGPoint preP = [touch previousLocationInView:self];
    
    // 获取x轴偏移量
    CGFloat offsetX = curP.x - preP.x;
    
    // 获取y轴偏移量
    CGFloat offsetY = curP.y - preP.y;
    
    // 修改view的位置（frame,center,transform）
    self.transform = CGAffineTransformTranslate(self.transform, offsetX, offsetY);
   
//    self.transform = CGAffineTransformMakeTranslation(offsetX, 0);
    
}

// 当手指离开这个view的时候
//- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event{
//     NSLog(@"%s",__func__);
//}

// 当触摸事件被打断的时候调用(电话打入)
- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
{
    
}
   ```
   
 #### 2.hitTest 的作用
 
 >   * （了解hitText）学习一个方法必须了解：什么时候调用和这个方法有什么用
    1. hitText什么时候调用:当一个事件传递给一个控件的时候，控件就会调用这个方法
    2. hitText作用: 寻找到最合适的view。
    * （回顾下事件传递），UIApplication -> UIWindow
    *  UIWindow去寻找最合适的view? [UIWindow hitTest:withEvent:]里面做了什么事情？
    1> 判断窗口能不能处理事件? 如果不能，意味着窗口不是最合适的view，而且也不会去寻找比自己更合适的view,直接返回nil,通知UIApplication，没有最合适的view。
    2> 判断点在不在窗口
    3> 遍历自己的子控件，寻找有没有比自己更合适的view
    4> 如果子控件不接收事件，意味着子控件没有找到最合适的view,然后返回nil,告诉窗口没有找到更合适的view,窗口就知道没有比自己更合适的view,就自己处理事件。
    * 验证下hitTest方法返回nil，里面的子控件能处理事件吗？ 重写根控制器view的hitTest:withEvent:方法，
    * 验证这个方法是否真能找到最合适的view？
    * 如果点击屏幕任何一个地方，都是白色的view，怎么做。直接返回白色的view,就不会继续去找白色view的子控件了。
    * 介绍pointInside方法
    * pointInside作用：判断一个点在不在一个控件上
    * point参数:方法调用者坐标系上的点，PPT画图分析原理。
    * 这节课的重点：学习完了pointInside,就能实现下hitTest方法底层是怎么做的了。
    
```

- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event
{
    
    // 当前控件上的点转换到chatView上
    CGPoint chatP = [self convertPoint:point toView:self.chatView];
    
    // 判断下点在不在chatView上
    if ([self.chatView pointInside:chatP withEvent:event]) {
        return self.chatView;
    }else{
        return [super hitTest:point withEvent:event];
    }  
}

- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event
{
    // 获取UITouch
    UITouch *touch = [touches anyObject];
    
    // 获取当前的点
    CGPoint curP = [touch locationInView:self];
    
    // 获取上一个的点
    CGPoint preP = [touch previousLocationInView:self];
    
    // 获取偏移量
    CGFloat offsetX = curP.x - preP.x;
    CGFloat OffsetY = curP.y - preP.y;
    
    // 修改控件的位置
    CGPoint center = self.center;
    center.x += offsetX;
    center.y += OffsetY;
    
    self.center = center;
}
    
```
#### 3.UIGestureRecognizer 的作用

```
 #pragma mark - 手势代理方法
// 是否允许开始触发手势
//- (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer
//{
//    return NO;
//}

// 是否允许同时支持多个手势，默认是不支持多个手势
// 返回yes表示支持多个手势
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer
{
    return YES;
}

// 是否允许接收手指的触摸点
//- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch{
//    // 获取当前的触摸点
//    CGPoint curP = [touch locationInView:self.imageView];
//    
//    if (curP.x < self.imageView.bounds.size.width * 0.5) {
//        return NO;
//    }else{
//        return YES;
//    }
//}


#pragma mark - 点按手势

- (void)setUpTap
{
    // 创建点按手势
    UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(tap:)];
    
    tap.delegate = self;
    
    [_imageView addGestureRecognizer:tap];
}

- (void)tap:(UITapGestureRecognizer *)tap
{
    NSLog(@"%s",__func__);
}

#pragma mark - 长按手势
// 默认会触发两次
- (void)setUpLongPress
{
    UILongPressGestureRecognizer *longPress = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPress:)];
    
    [self.imageView addGestureRecognizer:longPress];
}


- (void)longPress:(UILongPressGestureRecognizer *)longPress
{
    
    if (longPress.state == UIGestureRecognizerStateBegan) {
        
        NSLog(@"%s",__func__);
    }
}

#pragma mark - 清扫
- (void)setUpSwipe
{
    // 默认轻扫的方向是往右
    UISwipeGestureRecognizer *swipe = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipe)];
    
    swipe.direction = UISwipeGestureRecognizerDirectionUp;
    
    [self.imageView addGestureRecognizer:swipe];
    
    // 如果以后想要一个控件支持多个方向的轻扫，必须创建多个轻扫手势，一个轻扫手势只支持一个方向
    // 默认轻扫的方向是往右
    UISwipeGestureRecognizer *swipeDown = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipe)];
    
    swipeDown.direction = UISwipeGestureRecognizerDirectionDown;
    
    [self.imageView addGestureRecognizer:swipeDown];

    
}

- (void)swipe
{
    NSLog(@"%s",__func__);
}

#pragma mark - 旋转手势
- (void)setUpRotation
{
    UIRotationGestureRecognizer *rotation = [[UIRotationGestureRecognizer alloc] initWithTarget:self action:@selector(rotation:)];
    rotation.delegate = self;
    [self.imageView addGestureRecognizer:rotation];
}

// 默认传递的旋转的角度都是相对于最开始的位置
- (void)rotation:(UIRotationGestureRecognizer *)rotation
{
    
    self.imageView.transform = CGAffineTransformRotate(self.imageView.transform, rotation.rotation);
    
    // 复位
    rotation.rotation = 0;
    
    // 获取手势旋转的角度
    NSLog(@"%f",rotation.rotation);
}

#pragma mark - 捏合
- (void)setUpPinch
{
    UIPinchGestureRecognizer *pinch = [[UIPinchGestureRecognizer alloc] initWithTarget:self action:@selector(pinch:)];
    pinch.delegate = self;
    [self.imageView addGestureRecognizer:pinch];
}

- (void)pinch:(UIPinchGestureRecognizer *)pinch
{
    self.imageView.transform = CGAffineTransformScale(self.imageView.transform, pinch.scale, pinch.scale);
    
    // 复位
    
    pinch.scale = 1;
}

#pragma mark - 拖拽
- (void)setUpPan
{
    UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(pan:)];

    [self.imageView addGestureRecognizer:pan];
}

- (void)pan:(UIPanGestureRecognizer *)pan
{
    // 获取手势的触摸点
   // CGPoint curP = [pan locationInView:self.imageView];
    
    // 移动视图
    // 获取手势的移动，也是相对于最开始的位置
    CGPoint transP = [pan translationInView:self.imageView];
    
    self.imageView.transform = CGAffineTransformTranslate(self.imageView.transform, transP.x, transP.y);
    
    // 复位
    [pan setTranslation:CGPointZero inView:self.imageView];
    
  //  NSLog(@"%@",NSStringFromCGPoint(curP));
}
```