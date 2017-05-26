##### 1.整个APP中只有一个UIApplication 
```
#pragma mark - 设置提醒数字
- (void)application
{
    // 1.整个app中只有一个UIApplication
    
    //    UIApplication *app = [[UIApplication alloc] init];
    
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
##### 2.隐藏状态栏
