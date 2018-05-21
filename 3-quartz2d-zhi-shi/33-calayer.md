## 一.注意的东西

#### 1.属性
```
宽度和高度@property CGRect bounds;位置(默认指中点，具体由anchorPoint决定)@property CGPoint position;锚点(x,y的范围都是0-1)，决定了position的含义@property CGPoint anchorPoint;背景颜色(CGColorRef类型)@property CGColorRef backgroundColor;形变属性@property CATransform3D transform;边框颜色(CGColorRef类型)@property CGColorRef borderColor;边框宽度@property CGFloat borderWidth;圆角半径@property CGColorRef borderColor;内容(比如设置为图片CGImageRef)@property(retain) id contents;
```
#### 2.区别_重要
```
CALayer是定义在QuartzCore框架中的CGImageRef、CGColorRef两种数据类型是定义在CoreGraphics框架中的UIColor、UIImage是定义在UIKit框架中的其次QuartzCore框架和CoreGraphics框架是可以跨平台使用的，在iOS和Mac OS X上都能使用但是UIKit只能在iOS中使用为了保证可移植性，QuartzCore不能使用UIImage、UIColor，只能使用CGImageRef、CGColorRef
其实，对比CALayer，UIView多了一个事件处理的功能。也就是说，CALayer不能处理用户的触摸事件，而UIView可以
```
#### 3.CALayer有2个非常重要的属性
```
position和anchorPoint@property CGPoint position;用来设置CALayer在父层中的位置以父层的左上角为原点(0, 0)@property CGPoint anchorPoint;称为“定位点”、“锚点”决定着CALayer身上的哪个点会在position属性所指的位置以自己的左上角为原点(0, 0)它的x、y取值范围都是0~1，默认值为（0.5, 0.5）
```

## 二. 一般使用
#### 1.阴影效果 太阳
```
- (void)viewLayer
{
    // 设置阴影
    // Opacity：不透明度
    _redView.layer.shadowOpacity = 1;
    //    _redView.layer.shadowOffset = CGSizeMake(10, 10);
    // 注意：图层的颜色都是核心绘图框架，通常。CGColor
    _redView.layer.shadowColor = [UIColor yellowColor].CGColor;
    _redView.layer.shadowRadius = 10;
    // 圆角半径
    _redView.layer.cornerRadius = 50;
    // 边框
    _redView.layer.borderWidth = 1;
    _redView.layer.borderColor = [UIColor whiteColor].CGColor;
}
```
#### 2.圆角剪裁
```
- (void)imageLayer
{
    // cornerRadiu设置控件的主层边框
    _imageView.layer.cornerRadius = 50;
    
    NSLog(@"%@",_imageView.layer.contents)
    ;
    // 超出主层边框的内容全部裁剪掉
    _imageView.layer.masksToBounds = YES;
    
    // 设置边框
    _imageView.layer.borderColor = [UIColor whiteColor].CGColor;
    _imageView.layer.borderWidth = 1;
    
    // 如何判断以后是否需要裁剪图片，就判断下需要显示图层的控件是否是正方形。
}
```

#### 3.图片View 缩放
```
    // 图层形变
    // 缩放
    [UIView animateWithDuration:1 animations:^{
        
//        _redView.layer.transform = CATransform3DMakeRotation(M_PI, 1, 1, 0);
//        _redView.layer.transform = CATransform3DMakeScale(0.5, 0.5, 1);
        
        // 快速进行图层缩放,KVC
        // x,y同时缩放0.5
//        [_redView.layer setValue:@0.5 forKeyPath:@"transform.scale"];
        
        [_redView.layer setValue:@(M_PI) forKeyPath:@"transform.rotation"];
        
    }];
```
#### 4.隐式动画
```objc
#define angle2radion(angle) ((angle) / 180.0 * M_PI)
@interface ViewController ()
@property (nonatomic, weak) CALayer *layer;
@property (weak, nonatomic) IBOutlet UIView *blueView;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    CALayer *layer = [CALayer layer];
    
    layer.position = CGPointMake(200, 150);
    
//    layer.anchorPoint = CGPointZero;
    
    layer.bounds = CGRectMake(0, 0, 80, 80);
    
    layer.backgroundColor = [UIColor redColor].CGColor;
    
    [self.view.layer addSublayer:layer];
    
    _layer = layer;
}

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    // 旋转
        // 1 ~ 360
        
    _blueView.layer.transform = CATransform3DMakeRotation(angle2radion(arc4random_uniform(360) + 1), 0, 0, 1);
    
    _blueView.layer.position = CGPointMake(arc4random_uniform(200) + 20, arc4random_uniform(400) + 50);
    
    _blueView.layer.cornerRadius = arc4random_uniform(50);
    
    _blueView.layer.backgroundColor = [self randomColor].CGColor;
    
    _blueView.layer.borderWidth = arc4random_uniform(10);
    _blueView.layer.borderColor = [self randomColor].CGColor;
    
}

- (UIColor *)randomColor
{
    CGFloat r = arc4random_uniform(256) / 255.0;
    CGFloat b = arc4random_uniform(256) / 255.0;
    CGFloat g = arc4random_uniform(256) / 255.0;
    return [UIColor colorWithRed:r green:g blue:b alpha:1];
}
```

#### 5.时钟

```objc
//
//  ViewController.m
//  06-时钟(了解)
//
//  Created by xiaomage on 15/6/23.
//  Copyright (c) 2015年 xiaomage. All rights reserved.
//

#import "ViewController.h"
#define kClockW _clockView.bounds.size.width


#define angle2radion(a) ((a) / 180.0 * M_PI)
// 一秒钟秒针转6°
#define perSecondA 6

// 一分钟分针转6°
#define perMinuteA 6

// 一小时时针转30°
#define perHourA 30

// 每分钟时针转多少度
#define perMinuteHourA 0.5


@interface ViewController ()
@property (weak, nonatomic) IBOutlet UIImageView *clockView;

@property (nonatomic, weak) CALayer *secondLayer;

@property (nonatomic, weak) CALayer *minuteLayer;

@property (nonatomic, weak) CALayer *hourLayer;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    // 添加时针——————调用顺序
    [self setUpHourLayer];
    
    // 添加分针
    [self setUpMinuteLayer];
    
    // 添加秒针
    [self setUpSecondLayer];
    
    
    // 添加定时器
    [NSTimer scheduledTimerWithTimeInterval:1 target:self selector:@selector(timeChange) userInfo:nil repeats:YES];
    //——————主动调用时间 归位
    [self timeChange];
    
}

- (void)timeChange
{
    // 获取当前的系统的时间
    
    // 获取当前日历对象
    NSCalendar *calendar = [NSCalendar currentCalendar];
    
    // 获取日期的组件：年月日小时分秒
    // components:需要获取的日期组件
    // fromDate：获取哪个日期的组件
    // 经验：以后枚举中有移位运算符，通常一般可以使用并运算（|）
    NSDateComponents  *cmp = [calendar components:NSCalendarUnitSecond | NSCalendarUnitMinute | NSCalendarUnitHour fromDate:[NSDate date]];
    
    // 获取秒
    NSInteger second = cmp.second;
    
    // 获取分
    NSInteger minute = cmp.minute;
    
    // 获取小时
    NSInteger hour = cmp.hour;
    
    // 计算秒针转多少度
    CGFloat secondA = second * perSecondA;
    
    // 计算分针转多少度
    CGFloat minuteA = minute * perMinuteA;
    
    // 计算时针转多少度
    CGFloat hourA = hour * perHourA + minute * perMinuteHourA;
    
    // 旋转秒针
    _secondLayer.transform = CATransform3DMakeRotation(angle2radion(secondA), 0, 0, 1);
    
    // 旋转分针
    _minuteLayer.transform = CATransform3DMakeRotation(angle2radion(minuteA), 0, 0, 1);
    
    // 旋转小时
    _hourLayer.transform = CATransform3DMakeRotation(angle2radion(hourA), 0, 0, 1);
}
#pragma mark - 添加秒针
- (void)setUpSecondLayer
{
   CALayer *secondL = [CALayer layer];
    
    secondL.backgroundColor = [UIColor redColor].CGColor;
    
    // 设置锚点
    secondL.anchorPoint = CGPointMake(0.5, 1);
    
    secondL.position = CGPointMake(kClockW * 0.5, kClockW * 0.5);
    
    secondL.bounds = CGRectMake(0, 0, 1, kClockW * 0.5 - 20);
    
    
    
    [_clockView.layer addSublayer:secondL];
    
    _secondLayer = secondL;
}


#pragma mark - 添加分针
- (void)setUpMinuteLayer
{
    CALayer *layer = [CALayer layer];
    
    layer.backgroundColor = [UIColor blackColor].CGColor;
    
    // 设置锚点
    layer.anchorPoint = CGPointMake(0.5, 1);
    
    layer.position = CGPointMake(kClockW * 0.5, kClockW * 0.5);
    
    layer.bounds = CGRectMake(0, 0, 4, kClockW * 0.5 - 20);
    
    layer.cornerRadius = 4;
    
    
    [_clockView.layer addSublayer:layer];
    
    _minuteLayer = layer;
}

#pragma mark - 添加时针
- (void)setUpHourLayer
{
    CALayer *layer = [CALayer layer];
    
    layer.backgroundColor = [UIColor blackColor].CGColor;
    
    // 设置锚点
    layer.anchorPoint = CGPointMake(0.5, 1);
    
    layer.position = CGPointMake(kClockW * 0.5, kClockW * 0.5);
    
    layer.bounds = CGRectMake(0, 0, 4, kClockW * 0.5 - 40);
    
    layer.cornerRadius = 4;
    
    
    [_clockView.layer addSublayer:layer];
    
    _hourLayer = layer;
}


- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

```
