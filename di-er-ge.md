##遇到的简单理解方面的问题
######1.  .h文件中用@class 而不用#import"" 是因为程序APP 打开的时候要进行编译程序，@class  相对于#import 需要的时间少
######2. UIWidow 的底层实现 UIWiodws  是一个数组，我们开始讲window赋为主窗口,其实就是 调用Set 方法
######3. // stroyboard中Segue跳转到联系人界面  对应的标识Identifier
    [self performSegueWithIdentifier:@"login2Contact" sender:nil]