######主要逻辑关系就是DEMO 几个图片和一个 Scrollview 的方法

```
// 只要一滚动就会调用
- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    // 获取scrollView偏移量
    CGFloat offsetX = scrollView.contentOffset.x;
    
    int intOffsetX = (int)offsetX;
    
    NSString *imageName = [NSString stringWithFormat:@"520_userguid_person_taitou_%d",(intOffsetX % 2 + 1)];
    UIImage *image = [UIImage imageNamed:imageName];
    // 切换人物的图片
    _personView.image = image;
    
    // 旋转小太阳
    _sunView.transform = CGAffineTransformRotate(_sunView.transform, (5) / 180.0 * M_PI);
}
```