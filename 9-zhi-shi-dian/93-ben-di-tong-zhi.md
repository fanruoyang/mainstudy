```objc
先导入这个东西#import <UserNotifications/UserNotifications.h>

注册通知

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    // 使用 UNUserNotificationCenter 来管理通知
    UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
    //监听回调事件
    center.delegate = self;

    //iOS 10 使用以下方法注册，才能得到授权
    [center requestAuthorizationWithOptions:(UNAuthorizationOptionAlert + UNAuthorizationOptionSound + UNAuthorizationOptionBadge)
                          completionHandler:^(BOOL granted, NSError * _Nullable error) {
                              // Enable or disable features based on authorization.
                          }];


    return YES;
}


发送通知

// 使用 UNUserNotificationCenter 来管理通知
    UNUserNotificationCenter* center = [UNUserNotificationCenter currentNotificationCenter];

    //需创建一个包含待通知内容的 UNMutableNotificationContent 对象，注意不是 UNNotificationContent ,此对象为不可变对象。
    UNMutableNotificationContent* content = [[UNMutableNotificationContent alloc] init];
    content.title = [NSString localizedUserNotificationStringForKey:@"本地推送Title" arguments:nil];
    content.body = [NSString localizedUserNotificationStringForKey:@"本地推送Body"     arguments:nil];
    content.sound = [UNNotificationSound defaultSound];

    // 在 设定时间 后推送本地推送
    UNTimeIntervalNotificationTrigger* trigger = [UNTimeIntervalNotificationTrigger
                                                  triggerWithTimeInterval:5 repeats:NO];

    UNNotificationRequest* request = [UNNotificationRequest requestWithIdentifier:@"FiveSecond"
                                                                          content:content trigger:trigger];

    //添加推送成功后的处理！
    [center addNotificationRequest:request withCompletionHandler:^(NSError * _Nullable error) {

    }];

通知处理

实现UNUserNotificationCenterDelegate代理方法:

第一个方法：
-(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions))completionHandler{


    // 处理完成后条用 completionHandler ，用于指示在前台显示通知的形式
    completionHandler(UNNotificationPresentationOptionSound);
}
1
2
3
4
5
6
7
这个方法中的那句话就是，当应用在前台的时候，收到本地通知，是用什么方式来展现。系统给了三种形式：

typedef NS_OPTIONS(NSUInteger, UNNotificationPresentationOptions) {
    UNNotificationPresentationOptionBadge   = (1 << 0),
    UNNotificationPresentationOptionSound   = (1 << 1),
    UNNotificationPresentationOptionAlert   = (1 << 2),
} 

第二个代理方法：
这个方法是在后台或者程序被杀死的时候，点击通知栏调用的，在前台的时候不会被调用

- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler{

    UIAlertView *alert = [[UIAlertView alloc]initWithTitle:@"Title" message:@"message" delegate:self cancelButtonTitle:@"cancel" otherButtonTitles:@"ok", nil];
    [alert show];

    completionHandler();
}

```