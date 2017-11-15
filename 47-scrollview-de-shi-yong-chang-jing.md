#####1 视图向上滑动部分VIEW停止，其他VIew能够继续滚动-->内部控件悬停

     #pragma mark - <UIScrollViewDelegate>
- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    CGFloat imageH = self.imageView.frame.size.height;
    CGFloat offsetY = scrollView.contentOffset.y;
    
    NSLog(@"_________%f",offsetY);
    if (offsetY >= imageH) {
        // 将红色控件添加到控制器的view中，设置Y值为0
        CGRect redF = self.redView.frame;
        redF.origin.y = 0;
        self.redView.frame = redF;
        [self.view addSubview:self.redView];
    } else {
        // 将红色控件添加到scrollView中，设置Y值为图片的高度
        CGRect redF = self.redView.frame;
        redF.origin.y = 140;
        self.redView.frame = redF;
        [self.scrollView addSubview:self.redView];
    }
}

##### 2 下拉图片放大
     // if (offsetY < 0) {
    // CGFloat scale = 1 - (offsetY / 70);
    // self.imageView.transform = CGAffineTransformMakeScale(scale, scale);
    // }
    //下拉大与1 放大比例，可以用修改图片宽高来变
    CGFloat scale = 1 - (offsetY / 70);
    scale = (scale >= 1) ? scale : 1;
    self.imageView.transform = CGAffineTransformMakeScale(scale, scale); 