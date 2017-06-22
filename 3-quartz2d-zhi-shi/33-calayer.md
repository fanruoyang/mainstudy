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
#### 4.阴影效果 太阳
#### 5.阴影效果 太阳
