## 1.属性概念
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

