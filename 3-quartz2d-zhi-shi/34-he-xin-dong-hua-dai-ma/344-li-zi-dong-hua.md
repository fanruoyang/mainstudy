#04.粒子动画
效果：随机绘制一条路径，点击开始按钮，粒子动画

###实现思路
1.搞个画板绘制路径，自定义view

2.给自定义view添加pan手势，和创建复制图层和圆形图层，只需要设置一次，在awakeFromNib方法中设置。

```

	// 添加pan手势
    UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(pan:)];

    [self addGestureRecognizer:pan];

    // 创建复制图层
    CAReplicatorLayer *repLayer = [CAReplicatorLayer layer];

    repLayer.frame = self.bounds;

    [self.layer addSublayer:repLayer];


    // 创建粒子图层
    CALayer *layer = [CALayer layer];

    layer.cornerRadius = 5;

    layer.frame = CGRectMake(-100, 10, 10, 10);

    layer.backgroundColor = [UIColor whiteColor].CGColor;

    [repLayer addSublayer:layer];

    _dotLayer = layer;

```

3.因为核心动画只能设置一个路径，因此只能创建一个路径，懒加载路径。


```
- (UIBezierPath *)path
{
    if (_path == nil) {
        _path = [UIBezierPath bezierPath];
    }

    return _path;
}

```

4.在一开始拖动的时候，保存路径起点，设置路径起点，拖动的时候每次添加线到某个点。

```

	CGPoint curP = [pan locationInView:self];
    if (pan.state == UIGestureRecognizerStateBegan) {

        _startP = curP;

        [self.path moveToPoint:_startP];

    }

    [self.path addLineToPoint:curP];


    [self setNeedsDisplay];
```

5.路径绘制好了，点击开始按钮的时候，添加动画到图层


```
CAKeyframeAnimation *anim = [CAKeyframeAnimation animation];

    anim.keyPath = @"position";
    anim.duration = 4;
    anim.path = self.path.CGPath;

    anim.repeatCount = MAXFLOAT;

    [_dotLayer addAnimation:anim forKey:@"anim"];
    anim.delegate = self;

```

6.复制图层

```
	repLayer.instanceCount = 20;
    repLayer.instanceDelay = 4 / 20.0;

    // 设置子层颜色
    repLayer.instanceColor = [UIColor colorWithRed:0 green:1 blue:0 alpha:1].CGColor;

    // 设置子层颜色绿色通道偏移量
    repLayer.instanceGreenOffset = -0.03;

```
7.重绘
清空路径，重新绘制，移除图层动画。

```
	_path = nil;
    [_dotLayer removeAnimationForKey:@"anim"];
    [self setNeedsDisplay];
```
