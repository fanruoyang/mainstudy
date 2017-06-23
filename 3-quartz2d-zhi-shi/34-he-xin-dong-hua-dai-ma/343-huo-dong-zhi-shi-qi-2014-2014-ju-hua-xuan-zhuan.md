#03-活动指示器

### 实现思路
1.创建复制图层

```objc
	CAReplicatorLayer *replicator = [CAReplicatorLayer layer];
    replicator.frame = CGRectMake(50, 50, 200, 200);

    replicator.backgroundColor = [UIColor redColor].CGColor;

    [self.view.layer addSublayer:replicator];
```

2.创建一个矩形图层，设置缩放动画。

```objc
 	CALayer *indicator = [CALayer layer];

    indicator.transform = CATransform3DMakeScale(0, 0, 0);

    indicator.position = CGPointMake(100, 20);

    indicator.bounds = CGRectMake(0, 0, 10, 10);

    indicator.backgroundColor = [UIColor greenColor].CGColor;

    [replicator addSublayer:indicator];

    CGFloat durtion = 1;

    CABasicAnimation *anim = [CABasicAnimation animation];

    anim.keyPath = @"transform.scale";
    anim.fromValue = @1;
    anim.toValue = @0.1;

    anim.repeatCount = MAXFLOAT;

    anim.duration = durtion;

    [indicator addAnimation:anim forKey:nil];
```

3.复制矩形图层，并且设置每个复制层的角度形变

```objc
	int count = 10;

    // 设置子层次数
    replicator.instanceCount = count;



    // 设置子层形变角度
    CGFloat angle = M_PI * 2 / count;
    replicator.instanceTransform = CATransform3DMakeRotation(angle, 0, 0, 1);

```

4.设置复制动画延长时间（需要保证第一个执行完毕之后，绕一圈刚好又是从第一个执行，因此需要把动画时长平均分给每个子层）
公式:延长时间 = 动画时长 / 子层总数

假设有两个图层，动画时间为1秒，延长时间就为0.5秒。当第一个动画执行到一半的时候（0.5），第二个开始执行。第二个执行完

```objc
	// 设置子层动画延长时间
    replicator.instanceDelay = durtion / count;


```
