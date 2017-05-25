#### transform
```objc
    // transform:形变属性，能完成的功能：平移、缩放、旋转
    [UIView animateWithDuration:2.0 animations:^{
        // 缩放
       self.tempView.transform = CGAffineTransformMakeScale(0.5, 0.5);
       // 平移
       self.tempView.transform = CGAffineTransformMakeTranslation(-100, 100);
        // 旋转
       self.tempView.transform = CGAffineTransformMakeRotation(-M_PI_4);
       CGAffineTransform translation = CGAffineTransformMakeTranslation(-100, 100);
       CGAffineTransform scaleTranslation = CGAffineTransformScale(translation, 0.5, 0.5);
      CGAffineTransform rotateScaleTranslation = CGAffineTransformRotate(scaleTranslation, M_PI_2);
       self.tempView.transform = rotateScaleTranslation;
   }];
       // 清空transform，以前的平移、缩放、旋转都会消失
    [UIView animateWithDuration:2.0 animations:^{
        self.tempView.transform = CGAffineTransformIdentity;
    }];
    //点击屏幕
    - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    }
```
####转动的那个图片
- UIActivityIndicatorView
- Animating 是动作
- Hides When Stopped 为Yes 为当没有显示不动

## 代理
* 代理设计模式的作用:
    * 1.A对象监听B对象的一些行为，A成为B的代理
    * 2.B对象想告诉A对象一些事情，A成为B的代理

* 代理设计模式的总结：
    * 如果你想监听别人的一些行为，那么你就要成为别人的代理
    * 如果你想告诉别人一些事情，那么就让别人成为你的代理

* 代理设计模式的开发步骤
    * 1.拟一份协议（协议名字的格式：控件名 + Delegate），在协议里面声明一些代理方法（一般代理方法都是@optional）
    * 2.声明一个代理属性：@property (nonatomic, weak) id<代理协议> delegate;
    * 3.在内部发生某些行为时，调用代理对应的代理方法，通知代理内部发生什么事
    * 4.设置代理：xxx.delegate = yyy;
    * 5.yyy对象遵守协议，实现代理方法

## 代理和通知的区别
- 代理：1个对象只能告诉另1个对象发生了什么事
 ```objc
@class XMGLoadMoreFooter;
@protocol XMGLoadMoreFooterDelegate <NSObject>
@optional
-(void)loadMoreFooterDidClickLoadMoreButton:(XMGLoadMoreFooter *)footer;
@end
@property (nonatomic, weak) id<XMGLoadMoreFooterDelegate> delegate;
    // 告诉代理
    if ([self.delegate respondsToSelector:@selector(loadMoreFooterDidClickLoadMoreButton:)]) {
        [self.delegate loadMoreFooterDidClickLoadMoreButton:self];
    }
}
```
- 通知：1个对象可以告诉N个对象发生了什么事
```objc
//接收通知 然后走对应都text方法
    [[NSNotificationCenter defaultCenter] addObserver:self.p1 selector:@selector(test) name:@"TestNotification" object:nil];
    //发出通知
    [[NSNotificationCenter defaultCenter] postNotificationName:@"TestNotification" object:@"123"];
    //移除通知
    - (void)dealloc{
        [[NSNotificationCenter defaultCenter] removeObserver:self];
    }
```

## KVC\KVO
- KVC(Key Value Coding)常见作用：给模型属性赋值
- KVO(Key Value Observing)常用作用：监听模型属性值的改变
- KVO的使用步骤<br>

```objc
// cc监听了aa的name属性的改变
[aa addObserver:cc forKeyPath:@"name" options: NSKeyValueObservingOptionOld context:nil];

// cc得实现监听方法
/**
 * 当监听到object的keyPath属性发生了改变
 */
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context
{
    NSLog(@"监听到%@对象的%@属性发生了改变， %@", object, keyPath, change);
}
```
# Info.plist 文件操作

- 1.Bundle name  对应的名字一般指的是  12 个字符 软件软件软件 --APP 的显示名称
- 2.Bundle versions string, short  版本号 ---要一直增加
```
    NSDictionary *infoDict =  [NSBundle mainBundle].infoDictionary;   
    NSString *version = infoDict[@"CFBundleVersion"]; 
```
- 3.Bundle identifier APP 唯一标实 用在很多地方

#### .PCH 的文件的操作

- 1. 首先需要添加完成后 将文件路径填写进里面
