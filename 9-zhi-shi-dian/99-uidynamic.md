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
