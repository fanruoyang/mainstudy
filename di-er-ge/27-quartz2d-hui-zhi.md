###1. 基础

>// 什么时候调用:当这个View要显示的时候才会调用drawRect绘制图形，
// 注意：rect是当前控件的bounds
// 注意：drawRect不能手动调用，因为图形上下文我们自己创建不了，只能由系统帮我们创建，并且传递给我们

- 1. 画园 百分比

```
 - (void)setProgress:(CGFloat)progress
{
    _progress = progress;
    
    
    // 重新绘制圆弧
//    [self drawRect:self.bounds];
    
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

- 2.画饼图 简单的 随机数据没有写

```
- (void)drawRect:(CGRect)rect {
    // Drawing code
    
    NSArray *arr = [self arrRandom];
    CGFloat radius = rect.size.width * 0.5;
    CGPoint center = CGPointMake(radius, radius);    
    CGFloat startA = 0;
    CGFloat angle = 0;
    CGFloat endA = 0;
    

    for (int i = 0; i < arr.count; i++A) {
        startA = endA;
        angle = [arr[i] integerValue] / 100.0 * M_PI * 2;
        endA = startA + angle;
        UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:center radius:radius startAngle:startA endAngle:endA clockwise:YES];
         // 添加一根线到圆心
        [path addLineToPoint:center];
        // 描边和填充通用
        [[self colorRandom] set];
        
        [path fill];
    }   
}
```
- 3.画简单柱状图 X.Y.W.H

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

