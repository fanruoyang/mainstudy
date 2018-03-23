https://www.jianshu.com/p/e096d2dda478
##### UIDynamic使用步骤

- 创建一个物理仿真器（顺便设置仿真范围）
- 创建相应的物理仿真行为（顺便添加物理仿真元素）
- 将物理仿真行为添加到物理仿真器中，开始仿真
物理仿真元素

不是任何对象都能做物理仿真元素，不是任何对象都能进行物理仿真

##### 哪些对象才能做物理仿真元素？

任何遵守了UIDynamicItem协议的对象
UIView默认已经遵守了UIDynamicItem协议，因此任何UI控件都能做物理仿真
UICollectionViewLayoutAttributes类默认也遵守UIDynamicItem协议

##### 物理仿真行为

UIDynamic提供了以下几种物理仿真行为
UIGravityBehavior：重力行为
UICollisionBehavior：碰撞行为
UISnapBehavior：捕捉行为
UIPushBehavior：推动行为
UIAttachmentBehavior：附着行为
UIDynamicItemBehavior：动力元素行为
##### 物理仿真器

它可以让物理仿真元素执行物理仿真行为，它是UIDynamicAnimator类型的对象
UIDynamicAnimator的初始化
```
- (instancetype)initWithReferenceView:(UIView *)view;

view参数：是一个参照视图，表示物理仿真的范围

UIDynamicAnimator的常见方法

//添加1个物理仿真行为

- (void)addBehavior:(UIDynamicBehavior *)behavior;

//移除1个物理仿真行为

- (void)removeBehavior:(UIDynamicBehavior *)behavior;

//移除之前添加过的所有物理仿真行为

- (void)removeAllBehaviors;

UIDynamicAnimator的常见属性

//参照视图

@property (nonatomic, readonly) UIView* referenceView;

//添加到物理仿真器中的所有物理仿真行为

@property (nonatomic, readonly, copy) NSArray* behaviors;

//是否正在进行物理仿真
@property (nonatomic, readonly, getter = isRunning) BOOL running;

//代理对象（能监听物理仿真器的仿真过程，比如开始和结束）
@property (nonatomic, assign) id <UIDynamicAnimatorDelegate> delegate;
```


#####  简单代码

```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    // -1.移除仿真行为
    [self.animator removeAllBehaviors];
    
    // 0.获取用户点击的点
    CGPoint point = [[touches anyObject] locationInView:self.view];
    
    // 1.创建捕捉行为
    UISnapBehavior *snap = [[UISnapBehavior alloc] initWithItem:self.redView snapToPoint:point];
    
    // 1.1.设置阻力系数(0~1)
    snap.damping = 1;
    
    // 2.将捕捉行为添加到仿真器中(一个仿真器中只能存在一个捕捉行为)
    [self.animator addBehavior:snap];
}

- (void)gravityAndCollision
{
    // 1.创建仿真行为,并且指定仿真元素
    // 1.1.重力行为
    UIGravityBehavior *gravity = [[UIGravityBehavior alloc] initWithItems:@[self.redView]];
    
    // 1.2.设置重力的方向(默认角度M_PI_2)
    // gravity.angle = 0;
    
    // 1.3.设置重力的大小
    // gravity.magnitude = 10.0;
    
    // 1.4.设置重力的向量值
    // gravity.gravityDirection = CGVectorMake(1.0, 3.0);
    
    // 1.2.碰撞行为
    UICollisionBehavior *collision = [[UICollisionBehavior alloc] initWithItems:@[self.redView, self.blueView]];
    // 1.2.1.设置碰撞边界
    collision.translatesReferenceBoundsIntoBoundary = YES;
    
    // 1.2.2.给碰撞行为添加一个边界
    CGPoint startPoint = CGPointMake(0, self.view.bounds.size.height * 2 / 3);
    CGPoint endPoint = CGPointMake(self.view.bounds.size.width, self.view.bounds.size.height * 2 / 3);
    [collision addBoundaryWithIdentifier:@"lineBoundary" fromPoint:startPoint toPoint:endPoint];
    // [collision removeBoundaryWithIdentifier:@"lineBoundary"];
    UIBezierPath *bezierPath = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(0, 0, self.view.bounds.size.width, self.view.bounds.size.width)];
    [collision addBoundaryWithIdentifier:@"bezierPath" forPath:bezierPath];
    
    // 2.将仿真行为添加到仿真器中
    [self.animator addBehavior:gravity];
    [self.animator addBehavior:collision];
}

#pragma mark - 懒加载代码
- (UIDynamicAnimator *)animator
{
    if (_animator == nil) {
        // 创建仿真器,并且指定仿真范围
        self.animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];
    }
    return _animator;
}

```