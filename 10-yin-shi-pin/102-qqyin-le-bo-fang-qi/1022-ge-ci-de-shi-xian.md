##### 1 侧滑可以查看歌词，用Scrollview
- 设置宽度，分页
- 代理方法
```
- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    // 1.获取到滑动多少
    CGPoint point = scrollView.contentOffset;
    
    // 2.计算滑动的比例
    CGFloat ratio = 1 - point.x / scrollView.bounds.size.width;
    
    // 3.设置iconView和歌词的Label的透明度
    self.iconView.alpha = ratio;
    self.lrcLabel.alpha = ratio;
}

```