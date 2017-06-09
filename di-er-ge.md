##遇到的简单理解方面的问题
#####1.  .h文件中用@class 而不用#import"" 是因为程序APP 打开的时候要进行编译程序，@class  相对于#import 需要的时间少

#####2. UIWidow 的底层实现 UIWiodws  是一个数组，我们开始讲window赋为主窗口,其实就是 调用Set 方法

#####3. // stroyboard中Segue跳转到联系人界面  对应的标识Identifier
    [self performSegueWithIdentifier:@"login2Contact" sender:nil]
    调用Segue对象的- (void)perform;方法开始执行界面跳转操作
    - 如果segue的style是push 
       取得sourceViewController所在的UINavigationController
       调用UINavigationController的push方法将destinationViewController压入栈中，完成
    - 如果segue的style是modal   
    调用sourceViewController的presentViewController方法将destinationViewController展示出来
    
 - 故事版跳转传值跟代码相似 ___顺传值

```
/*  
 1.[self performSegueWithIdentifier]
 2.创建segue
 3.设置来源控制器segue.sourceViewController = self
 4.创建目的控制器，segue.destinationViewController = 目的控制器
 5.[self prepareForSegue]跳转之前的准备操作
 6.[segue perform]
 7.判断下segue的类型，如果是push,拿到导航控制器push
 [self.navigationController pushViewController:segue.destinationViewController animated:YES];
 */
@implementation XMGLoginViewController

// 在执行跳转之前的时候调用
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender
{
    UIViewController *vc = segue.destinationViewController;
    vc.title = [NSString stringWithFormat:@"%@的联系人列表", _accountField.text];
    NSLog(@"%@--%@",segue.sourceViewController,segue.destinationViewController);
}

```
#####4. // UITextField 的默认点击状态 beconmeFirstResponder___已经编辑     
#####5. // UITextField 的默认不能编辑跟BUTTON一样 enabled 为NO

#####6. // Push 和Modal 的区别在于一个是有关系——左右。Modal 没关系冲下方出来

  - Modal
  
     >// modal一个控制器
    [self presentViewController:vc animated:YES completion:nil];
        // modal出谁，谁就有权利dismiss
    // 谁modal控制器，谁也有权利dismiss
    //[self dismissViewControllerAnimated:YES completion:nil];
    
  - Push 
    push过来对应的Pop回来
    
 #####7.宏的作用
  // 宏里面的#，会自动把后面的参数变成C语言的字符串
        #define keyPath(objc,keyPath) @(((void)objc.keyPath,#keyPath))

  // 宏的操作原理，每输入一个字母就会直接把宏右边的拷贝，并且会自动补齐前面的内容。