##### Socket

- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    if (scrollView.contentOffset.y == 0) {
        self.title = @"我的";
        
    }else {
        self.title = nil;
    }
    
    CGFloat Offset_y = scrollView.contentOffset.y;
    
    if (Offset_y < 0) {
        CGFloat totalOffset = pictureHeight - Offset_y;
        CGFloat scale = totalOffset / pictureHeight;
        CGFloat width = Main_Width;
        _backview.frame = CGRectMake(-(width*scale-width)/2, Offset_y, width*scale, totalOffset);
        A=(width*scale-width)/2;
        NSLog(@"a%f",A);
        
        
    }
}

