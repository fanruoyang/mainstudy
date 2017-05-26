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