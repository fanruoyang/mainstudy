# xcode 操作
- 1.快捷方法代码

   command+d  快捷复制
####注意点
- 1.按钮的不点击状态
```objc
self.removeBtn.enabled = NO;
```

- 2.获得对应的view的数量
```objc
//相当与获取对应当前视图的view 默认为0
NSUInteger index = self.shopsView.subviews.count;
```
- 3.％的算法
```objc
//这个是显示除法后的余数 根据这个计算行数
NSUInteger col = index % cols;
```
- 4.延时时间的几种方法
   - 方法1：performSelector
```objc
// 1.5s后自动调用self的hideHUD方法
//此方式要求必须在主线程中执行，否则无效。是一种非阻塞的执行方式，暂时未找到取消执行的方法
[self performSelector:@selector(hideHUD) withObject:nil afterDelay:1.5];
```
   - 方法2：GCD
```objc
    //此方式在可以在参数中选择执行的线程。是一种非阻塞的执行方式，没有找到取消执行的方法。
   dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
    // 1.5s后自动执行这个block里面的代码
    self.hud.alpha = 0.0;
});
```
   - 方法3：NSTimer
```objc
//inderval 是指的时间 userinfo 此参数可以为nil，当定时器失效时，由你指定的对象保留和释放该定时器
//当YES时，定时器会不断循环直至失效或被释放，当NO时，定时器会循环发送一次就失效。
//__此方式要求必须在主线程中执行，否则无效。
    self.timermoney=[NSTimer scheduledTimerWithTimeInterval:12 target:self selector:@selector(timerFired:)  userInfo:nil repeats:true];
    [[NSRunLoop currentRunLoop]addTimer:self.timermoney forMode:NSDefaultRunLoopMode];
    //立即执行
    [self timerFired:self.timermoney];
    //触发事件
-(void)timerFired:(NSTimer *)theTimer {
    //然后在需要结束的时候停止计时器 ,这个是唯一一个可以将计时器从runloop中移出的方法。
    [self.timermoney invalidate];
}
```
   - 4.sleep方式 没用过
  ```objc
  //此方式在主线程和子线程中均可执行。是一种阻塞的执行方式，建方放到子线程中，以免卡住界面,没有找到取消执行的方法。
  [NSThread sleepForTimeInterval:1.0f]; [self delayMethod];
  ```
- 5 本地数据的读取加载plist
```objc
      //NSBundle的作用：用来访问与之对应的资源内部的文件，可以获取文件的全路径
        //[NSBundle mainBundle]关联的就是项目的主资源包
        //NSBundle*bundle=[NSBundle mainBundle];
        //NSString *file=[bundle pathForResource:@"shops" ofType:@"plist"];
        //self.shops=[NSArray arrayWithContentsOfFile:file];
        NSArray *dictArray = [NSArray arrayWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"shops" ofType:@"plist"]];
```
- 6 点语法是get语法，加载数据的时候,不一定全部可以替换为self.      如果替换错误，容易造成无限循环 _这里要注意的是懒加载加载一些数据很好_

- 7.常见问题
   - 项目里面的某个.m文件无法使用
    - 检查：Build Phases -> Compile Sources
   - 项目里面的某个资源文件（比如plist、音频等）无法使用
    - 检查：Build Phases -> Copy Bundle Resources

##懒加载
```objc
- (NSArray *)shops
{
    if (_shops == nil) {
        // 加载一个字典数组
        NSArray *dictArray = [NSArray arrayWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"shops" ofType:@"plist"]];
        NSMutableArray *shopArray = [NSMutableArray array];
        for (NSDictionary *dict in dictArray) {
            XMGShop *shop = [XMGShop shopWithDict:dict];
            [shopArray addObject:shop];
        }
        _shops = shopArray;
    }
    return _shops;
}

```
## 模型
- 什么是模型？
    - 专门用来存放数据的对象
    - 一般都是一些直接继承自NSObject的纯对象
    - 内部会提供一些属性来存放数据
    ```objc
     XMGShop *shop = [XMGShop shopWithDict:dict];
     .h
     #import <Foundation/Foundation.h>
     @interface XMGShop : NSObject
     /** 商品名称 */
     @property (nonatomic, strong) NSString *name;
     /** 图标 */
     @property (nonatomic, strong) NSString *icon;
     //正规的方法a.进行数据存储b.将数据放好
     - (instancetype)initWithDict:(NSDictionary *)dict;
     + (instancetype)shopWithDict:(NSDictionary *)dict;
     @end
     .m
     #import "XMGShop.h"
     @implementation XMGShop
     - (instancetype)initWithDict:(NSDictionary *)dict
     {
      if (self = [super init]) {
      self.name = dict[@"name"];
      self.icon = dict[@"icon"];
      }
       return self;
     }
     + (instancetype)shopWithDict:(NSDictionary *)dict
     {
     return [[self alloc] initWithDict:dict];
     }
     @end
    ```
- 8.解析数据注意问题
  - instancetype在类型表示上，跟id一样，可以表示任何对象类型，进行对象操作

  - instancetype只能用在返回值类型上，不能像id一样用在参数类型上

  - instancetype比id多一个好处：编译器会检测instancetype

- 9.封装view 遇到的问题
   - 在initWithFrame:方法中添加子控件，提供便利构造方法
   ```objc
   +(instancetype)shopView
   {
    return [[self alloc] init];
    }
   ```
   - 在layoutSubviews方法中设置子控件的frame（一定要调用super的layoutSubviews）
   - 增加模型属性，在模型属性set方法中设置数据到子控件上
```objc
   // 创建一个父控件（整体：存放图片和文字）类方法
    XMGShopView *shopView = [XMGShopView shopView];
  // 给商品控件传递商品模型
    shopView.shop = self.shops[index];
// 这个方法专门用来布局子控件，一般在这里设置子控件的frame当控件本身的尺寸发生改变的时候，系统会自动调用这个方
    - (void)layoutSubviews{
    // 一定要调用super的layoutSubviews
    [super layoutSubviews];
    //这里写view上面的控件frame
    }
```
- 10.xib的加载
```objc
 - 注意的情况是如果View添加了xib 不会添加这个方法，这个方法是代码的-(instancetype)initwithframe:(cgrect)farme
 //xib 的方法 a是自定义的
 + (instancetype)pageView
{
    return [[[NSBundle mainBundle] loadNibNamed:NSStringFromClass(self) owner:nil options:nil] lastObject];
}
//b -（id）initwithcoder:(NSCoder *)aDecoder___调用b方法后会调用c方法 一般来说直接调用c方法就行
//c -(void)awakeFramNib{
}
//方法1
NSArray *views = [[NSBundle mainBundle] loadNibNamed:@"xib文件名" owner:nil options:nil]
//方法2
UINib *nib = [UINib nibWithNibName:@"xib文件名" bundle:nil];
NSArray *views = [nib instantiateWithOwner:nil options:nil];
```

![](/assets/Snip20150602_300.png)
![](/assets/Snip20150602_152.png)
