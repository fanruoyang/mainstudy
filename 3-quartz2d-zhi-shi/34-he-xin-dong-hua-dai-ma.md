![](/assets/QQ20170622-172851-HD.mp4)## 1.属性概念
## 2.简单功能
##### 1.心跳
```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    // 创建动画
    CABasicAnimation *anim = [CABasicAnimation animation];
    
    // 描述下修改哪个属性产生动画
    // anim.keyPath = @"position";
    // 只能是layer属性
    anim.keyPath = @"transform.scale";
    
    // 设置值
    // anim.toValue = [NSValue valueWithCGPoint:CGPointMake(250, 500)];
    anim.toValue = @0.5;
    // 设置动画执行次数
    anim.repeatCount = MAXFLOAT;
    
    // 取消动画反弹
    // 设置动画完成的时候不要移除动画
    anim.removedOnCompletion = NO;
    
    // 设置动画执行完成要保持最新的效果
    anim.fillMode = kCAFillModeForwards;
    
    [_imageV.layer addAnimation:anim forKey:nil];
    
}
```
##### 2.图片随着路径移动 DEMO

##### 3.转场动画 
![](/assets/效果.png)
```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    
    // 转场代码
    if (i == 4) {
        i = 1;
    }
    // 加载图片名称
    NSString *imageN = [NSString stringWithFormat:@"%d",i];
    
    _imageView.image = [UIImage imageNamed:imageN];
    
    i++;
    
    // 转场动画
    CATransition *anim = [CATransition animation];
    
    anim.type = @"pageCurl";
    
    anim.duration = 2;
    
    [_imageView.layer addAnimation:anim forKey:nil];
    
}
```
##### 4.动画组 __多个动作
```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    // 同时缩放，平移，旋转
    CAAnimationGroup *group = [CAAnimationGroup animation];
    
    CABasicAnimation *scale = [CABasicAnimation animation];
    scale.keyPath = @"transform.scale";
    scale.toValue = @0.5;
    
    CABasicAnimation *rotation = [CABasicAnimation animation];
    rotation.keyPath = @"transform.rotation";
    rotation.toValue = @(arc4random_uniform(M_PI));
    
    CABasicAnimation *position = [CABasicAnimation animation];
    position.keyPath = @"position";
    position.toValue = [NSValue valueWithCGPoint:CGPointMake(arc4random_uniform(200), arc4random_uniform(200))];
    
    group.animations = @[scale,rotation,position];
    
    [_redView.layer addAnimation:group forKey:nil];
    
    
}

```
##### 5. UIView和核心动画区别
```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
//    CABasicAnimation *anim = [CABasicAnimation animation];
//    
//    anim.keyPath = @"position";
//    
//    anim.toValue = [NSValue valueWithCGPoint:CGPointMake(150, 400)];
//    
//    // 注意：取消反弹代码必须放在图层添加动画之前。
//    anim.removedOnCompletion = NO;
//    
//    anim.fillMode = kCAFillModeForwards;
//    
//    anim.delegate = self;
//    
//    [_redView.layer addAnimation:anim forKey:nil];
    
    [UIView animateWithDuration:0.25 animations:^{
        
        _redView.layer.position = CGPointMake(150, 400);
    }
                     completion:^(BOOL finished) {
            NSLog(@"%@", NSStringFromCGPoint(_redView.layer.position));
        }];
    
    
}

// 注意：核心动画一切都是假象，并不会真实的改变图层的属性值，如果以后做动画的时候，不需要与用户交互，通常用核心动画（转场）。

// UIView动画必须通过修改属性的真实值，才有动画效果。

// 动画完成的时候调用
- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag
{
    NSLog(@"%@", NSStringFromCGPoint(_redView.layer.position));
}
```
##### 6. 转盘效果__DEMO

##### 7. 折叠图片__DEMO

##### 8. 音量震动条__DEMO

##### 9. 菊花旋转 下载__DEMO

##### 10. 粒子效果——传递__DEMO


