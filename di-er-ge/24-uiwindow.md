####1. 程序开始的时候的简单设置
                 ```
// 程序启动完成的时候
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    
    // 1.创建窗口,注意窗口必须要有尺寸，尺寸跟屏幕一样大的尺寸,窗口不要被释放
    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
    // 2.创建窗口的跟控制器
    UIViewController *vc = [[UIViewController alloc] init];
    // 如果设置窗口的跟控制器，默认就会把控制器的view添加到窗口上
    // 设置窗口的跟控制器，默认就有旋转功能
    self.window.rootViewController = vc;
    // 3.显示窗口 
    [self.window makeKeyAndVisible];
    
    return YES;
}
```
####2. UIWindow的层级关系 UIView 和UIViewController 也有
     - UIVindow 一般不用
         
      // 窗口是有层级关系
      // UIWindowLevelNormal < UIWindowLevelStatusBar < UIWindowLevelAlert
      // 设置窗口的层级关系
      self.window1.windowLevel = UIWindowLevelStatusBar;
      
####3. UINavigationController 的使用

    // 导航控制器也需要一个根控制器
    // 默认导航控制器把根控制器的view添加到导航控制器的view上
    UINavigationController *navVc = [[UINavigationController alloc] initWithRootViewController:vc];
    
- 出栈 返回跟控制器和上一个控制器，或者是对应的控制器

     控制器push到下一个控制器

```
   //1.对应的控制器
    for (UIViewController * controller in  self.navigationController.viewControllers) {//遍历
        if ([controller isKindOfClass:[HomeViewController class]])
        { //这里判断是否为你想要跳转的页面
            [self.navigationController popToViewController:controller animated:YES];
        }else{
            [self.tabBarController setSelectedIndex:0];
        }
    }
   //2.返回上一个控制器
   // pop不是马上把控制器销毁，
   // 回到上一个界面
    [self.navigationController popViewControllerAnimated:YES]; 
    
   //3.返回根控制器
   // 注意：只能返回到栈里面的控制器
    [self.navigationController popToViewController:self.navigationController.childViewControllers[0] animated:YES];
   //[self.navigationController popToRootViewControllerAnimated:YES];
```
####4. UINavigationController 导航栏内容
