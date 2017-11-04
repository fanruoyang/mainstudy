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

###### a 当前类或者他的子类第一次使用的时候才会调用      + (void)initialize{}

- 1.自定义左右按钮，注意一点是调用苹果自带的时候图片会有渲染状态，需要取消选择模式
      // 通过代码告诉苹果不要渲染图片
      image = [image imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];
- 2.通过设置图片Xcode
      打开图片，右面设置  Render AS ———>Original Image 可以了

- 3.可以跳转返回按钮文字的偏移量  -->一般不用
   [[UIBarButtonItem appearance] setBackButtonTitlePositionAdjustment:UIOffsetMake(0, -100) forBarMetrics:UIBarMetricsDefault];
   
   
####5. UIViewController 的生命周期
- 1.需要了解的有5个周期 
```
// 控制器的view加载完成的时候调用
  - (void)viewDidLoad {
    // super -> UIViewController
    [super viewDidLoad];
  }
  // 控制器的view即将显示的时候调用
   - (void)viewWillAppear:(BOOL)animated{
    [super viewWillAppear:animated];
    }
    /*
     viewDidAppear
     viewWillDisappear 
     viewDidDisappear 
     viewWillLayoutSubviews
     */
   //非ARC的时候有即将销毁和完全销毁俩个状态  
```
####6. UITextField 的使用注意点
- 1.监听文本改变

     // 给文本框添加监听器,及时监听文本框内容的改变
     [_accountField addTarget:self action:@selector(textChange) forControlEvents:UIControlEventEditingChanged];

- 2.代理方法不能立即监听，输入第二个的时候会显示第一个字符

    // 当用户输入的时候就会调用，判断下用户是否允许输入
    // 及时的判断文本框有没有内容
    // 注意这个方法不能及时获取文本框的内容  
    -(BOOL)textField:(UITextField *)textField   shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string
{
    
    NSLog(@"%@",_accountField.text);
    
    return YES;
}