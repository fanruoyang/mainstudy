##### 1.整个APP中只有一个UIApplication 
```
#pragma mark - 设置提醒数字
- (void)application
{
    // 1.整个app中只有一个UIApplication
      
    UIApplication *app = [UIApplication sharedApplication];
    
    // 2.UIApplication一般用来做一些应用级别的操作（app的提醒框，联网状态，打电话，打开网页，控制状态栏）
    //    UIApplication *app1 = [UIApplication sharedApplication];
    
    // 设置appIcon提醒数字，必须注册用户通知
    app.applicationIconBadgeNumber = 10;
    // 创建用户通知
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeBadge categories:nil];
    // 注册用户的通知
    [app registerUserNotificationSettings:settings];
    
    // 设置联网状态
    app.networkActivityIndicatorVisible = YES;

}
```
##### 2.隐藏状态栏 带动画效果
      [[UIApplication sharedApplication] setStatusBarHidden:YES withAnimation:UIStatusBarAnimationSlide];
##### 3.修改状态栏背景
    - 1.在Info.plist中设置UIViewControllerBasedStatusBarAppearance 为NO
    - 2.在需要改变状态栏颜色的 AppDelegate中在 didFinishLaunchingWithOptions 方法中增加： 
    [[UIApplication sharedApplication] setStatusBarStyle:UIStatusBarStyleLightContent];
    //如果需要在单个ViewController中添加，在ViewDidLoad方法中增加：
    [[UIApplication sharedApplication] setStatusBarStyle:UIStatusBarStyleLightContent];
    
##### 4.UIApplication 的代理方法和生命周期
```
// 学习代理方法，只需要知道这个什么时候调用，这个方法可以用来干嘛
// 程序启动完成的时候调用
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    NSLog(@"%s",__func__);
    return YES;
}

// 当app失去焦点的时候调用
- (void)applicationWillResignActive:(UIApplication *)application {
        NSLog(@"%s",__func__);
    // Sent when the application is about to move from active to inactive state. This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message) or when the user quits the application and it begins the transition to the background state.
    // Use this method to pause ongoing tasks, disable timers, and throttle down OpenGL ES frame rates. Games should use this method to pause the game.
}

// app进入后台的时候调用
// app忽然打断的时候，在这里保存一些需要用到的数据
- (void)applicationDidEnterBackground:(UIApplication *)application {
        NSLog(@"%s",__func__);
    // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
    // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
}
// app进入即将前台
- (void)applicationWillEnterForeground:(UIApplication *)application {
        NSLog(@"%s",__func__);
    // Called as part of the transition from the background to the inactive state; here you can undo many of the changes made on entering the background.
}
// 当app获取到焦点的时候调用，意味着app可以与用户交互
- (void)applicationDidBecomeActive:(UIApplication *)application {
        NSLog(@"%s",__func__);
    // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
}
// app被关闭的时候调用
- (void)applicationWillTerminate:(UIApplication *)application {
        NSLog(@"%s",__func__);
    // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.
}
// app接收到内存警告的时候调用
// 清空图片的缓存
- (void)applicationDidReceiveMemoryWarning:(UIApplication *)application
{
    NSLog(@"%s",__func__);
}
```
####### 模拟器有个功能可以模拟内存警告 Hardware-> Memory Warning
#####  应用程序的启动过程
```
// main:程序入口
int main(int argc, char * argv[]) {
    @autoreleasepool {
        // nil:@"UIApplication"
        
        // 类名转字符串好处: 1.防止输入错误
        
        // 字符串转类名 NSClassFromString(@"UIApplication")
        
        // 第三个参数：UIApplication
        // 第四个参数：AppDelegate：必须要遵守UIApplicationDelegate协议
        return UIApplicationMain(argc, argv, nil,NSStringFromClass([AppDelegate class]));
    }
}
/* UIApplicationMain底层实现
 1.根据principalClassName提供类名创建UIApplication对象
 2.创建UIApplicationDelegate对象，并且成为UIApplication对象代理，app.delete = delegate
 3.开启一个主运行循环，处理事件，可以保持程序一直运行。
 4.加载info.plist，并且判断有木有指定main.storyboard,如果指定，就会去加载
```