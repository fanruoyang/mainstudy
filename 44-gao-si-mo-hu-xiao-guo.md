## 控制器父子关系的建立原则
- 如果2个控制器的view是父子关系(不管是直接还是间接的父子关系)，那么这2个控制器也应该为父子关系

```objc
[a.view addSubview:b.view];
[a addChildViewController:b];
// 或者
[a.view addSubview:otherView];
[otherView addSubbiew.b.view];
[a addChildViewController:b];
```

#### 获得所有的子控制器 添加的时候用到
```objc
@property(nonatomic,readonly) NSArray *childViewControllers;
```

## 添加一个子控制器
```objc
//XMGOneViewController成为了self的子控制器
//self成为了XMGOneViewController的父控制器
[self addChildViewController:[[XMGOneViewController alloc] init]];
// 通过addChildViewController添加的控制器都会存在于childViewControllers数组中
```

#### 获得父控制器 当前的父控制器
```objc
@property(nonatomic,readonly) UIViewController *parentViewController;
```

## 将一个控制器从它的父控制器中移除
```objc
// 控制器a从它的父控制器中移除
[a removeFromParentViewController];
```

####  知识点 1
```objc
 /**
 * 当一个控制器从父控制器中移除时。会自动调用控制器的didMoveToParentViewController:方法，并且参数是nil
 * 当此控制器添加到父控制器中的时候会调用这个方法-->系统的会自动实现，如果使用
 * [self addChildViewController:[[XMGOneViewController alloc] init]];
 * [self.childViewControllers[0] didMoveToParentViewController:self]; 这相当入自己加入的控制器，需要自己添加这个方法，然后调用
 *  相当于监听添加到那个控制器，或者从哪个控制移除
 */
- (void)didMoveToParentViewController:(UIViewController *)parent
{
    [super didMoveToParentViewController:parent];
    
    NSLog(@"XMGOneViewController - 被添加到父控制器中 - %@", parent);
}
```