#### 1.基础 
>// 为什么要再drawRect里面绘图，只有在这个方法里面才能获取到跟View的layer相关联的图形上下文
// 什么时候调用:当这个View要显示的时候才会调用drawRect绘制图形，
// 注意：rect是当前控件的bounds
// 注意：drawRect不能手动调用，因为图形上下文我们自己创建不了，只能由系统帮我们创建，并且传递给我们

#### 2.绘制圆加下载进度

```
- (void)setProgress:(CGFloat)progress
{
    _progress = progress;   
    // 重新绘制圆弧
    //[self drawRect:self.bounds];  
    
    // 重绘，系统会先创建与view相关联的上下文，然后再调用drawRect
    [self setNeedsDisplay];
}
// 注意：drawRect不能手动调用，因为图形上下文我们自己创建不了，只能由系统帮我们创建，并且传递给我们

- (void)drawRect:(CGRect)rect {
    // Drawing code
    
    // 创建贝瑟尔路径
    CGFloat radius = rect.size.width * 0.5;
    CGPoint center = CGPointMake(radius, radius); 
    CGFloat endA = -M_PI_2 + _progress * M_PI * 2;
    
    UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:center radius:radius - 2 startAngle:-M_PI_2 endAngle:endA clockwise:YES];  
    [path stroke];   
}
```
#### 3.画饼图 随机的看demo

```
- (void)drawRect:(CGRect)rect {
    // Drawing code
    
    NSArray *arr = [self arrRandom];
    CGFloat radius = rect.size.width * 0.5;
    CGPoint center = CGPointMake(radius, radius);
   
    CGFloat startA = 0;
    CGFloat angle = 0;
    CGFloat endA = 0;

    for (int i = 0; i < arr.count; i++) {
        startA = endA;
        angle = [arr[i] integerValue] / 100.0 * M_PI * 2;
        endA = startA + angle;
        UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:center radius:radius startAngle:startA endAngle:endA clockwise:YES];
        
        [path addLineToPoint:center];
        
        [[self colorRandom] set];
        
        [path fill];
    }        
}
```

#### 4.绘制柱形图
```
- (void)drawRect:(CGRect)rect {
    // Drawing code
    
    NSArray *arr = [self arrRandom];  
    CGFloat x = 0;
    CGFloat y = 0;
    CGFloat w = 0;
    CGFloat h = 0;
    
    for (int i = 0; i < arr.count; i++) {
        
        w = rect.size.width / (2 * arr.count - 1);
        x = 2 * w * i;
        h = [arr[i] floatValue] / 100.0 * rect.size.height;
        y = rect.size.height - h;
             
        UIBezierPath *path = [UIBezierPath bezierPathWithRect:CGRectMake(x, y, w, h)];      
        [[self colorRandom] set];     
        [path fill];      
    }
    
}
```

#### 5.绘制阴影空心文字Label 
![](/assets/WX20170609-184834.png)

```
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    NSMutableDictionary *textDict = [NSMutableDictionary dictionary];
    
    // 设置文字颜色
    textDict[NSForegroundColorAttributeName] = [UIColor redColor];
    
    // 设置文字字体
    textDict[NSFontAttributeName] = [UIFont systemFontOfSize:30];
    
    // 设置文字的空心颜色和宽度
    textDict[NSStrokeWidthAttributeName] = @3;
    
    textDict[NSStrokeColorAttributeName] = [UIColor yellowColor];
    
    // 创建阴影对象
    NSShadow *shadow = [[NSShadow alloc] init];
    shadow.shadowColor = [UIColor greenColor];
    shadow.shadowOffset = CGSizeMake(4, 4);
    shadow.shadowBlurRadius = 3;
    textDict[NSShadowAttributeName] = shadow;
    // 创建富文本字符串
    NSAttributedString *strArr = [[NSAttributedString alloc] initWithString:@"asdlsajkldl" attributes:textDict];
    _labelView.attributedText =  strArr;
    
    // 只能计算普通文本框
//    [_labelView sizeToFit];
}
```
#### 5.绘制图片 Image 都在UIView中绘制
```
   // 超出裁剪区域的内容全部裁剪掉
    // 注意：裁剪必须放在绘制之前
    UIRectClip(CGRectMake(0, 0, 50, 50));
    // 默认绘制的内容尺寸跟图片尺寸一样大,跟控件大小不合适，下面那个方法是适应
   //[image drawAtPoint:CGPointZero];
  [image drawInRect:rect];
 // 图片平铺
   [image drawAsPatternInRect:rect];
```
#### 5.定时器 雪花下落
```
- (void)drawRect:(CGRect)rect {
    // 如果以后想绘制东西到view上面，必须在drawRect方法里面，不管有没有手动获取到上下文
    // 修改雪花y值
    UIImage *image =  [UIImage imageNamed:@"雪花"];
    [image drawAtPoint:CGPointMake(50, _snowY)];
    _snowY += 10;
    //这个是重新调用循环
    if (_snowY > rect.size.height) {
        _snowY = 0;
    }
}

// 如果在绘图的时候需要用到定时器，通常

// NSTimer很少用于绘图，因为调度优先级比较低，并不会准时调用
- (void)awakeFromNib
{
    [super awakeFromNib];
    // 创建定时器 这个会卡顿
//    [NSTimer scheduledTimerWithTimeInterval:0.1 target:self selector:@selector(timeChange) userInfo:nil repeats:YES];
    CADisplayLink *link = [CADisplayLink displayLinkWithTarget:self selector:@selector(timeChange)];
    // 添加主运行循环
    [link addToRunLoop:[NSRunLoop mainRunLoop] forMode:NSDefaultRunLoopMode];
}

// CADisplayLink:每次屏幕刷新的时候就会调用，屏幕一般一秒刷新60次

- (void)timeChange
{
    // 注意：这个方法并不会马上调用drawRect,其实这个方法只是给当前控件添加刷新的标记，等下一次屏幕刷新的时候才会调用drawRect
    [self setNeedsDisplay];
}

```

#### 5.矩阵操作 ___  旋转椭圆
```
- (void)drawRect:(CGRect)rect {
    // 1.获取上下文
    CGContextRef ctx = UIGraphicsGetCurrentContext();
    // 2.描述路径
   UIBezierPath *path = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(-100, -50, 200, 100)];
    
    [[UIColor redColor] set];
    // 上下文矩阵操作
    // 注意:矩阵操作必须要在添加路径之前
    //  平移
    CGContextTranslateCTM(ctx, 100, 50);
    // 缩放
    CGContextScaleCTM(ctx, 0.5, 0.5);
    // 旋转
    CGContextRotateCTM(ctx, M_PI_4);
    // 3.把路径添加上下文
    CGContextAddPath(ctx, path.CGPath);
    [[UIColor redColor] set];
    // 4.渲染上下文
    CGContextFillPath(ctx);
}
```

#### 6.添加水印 