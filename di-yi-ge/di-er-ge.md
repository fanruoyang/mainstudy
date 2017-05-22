# scrollview
####1.属性介绍
- 1 .获取图片的大小
```objc
    uiimagView.image.frame.size.with==实际宽
    //这个方法可以直接获取当前图片的大小，比上面的方便
     UIImageView *imageView=[UIImageView alloc]initWithImage:[UIImage imageNamed:@""];
```

- 2.模拟俩根手指  //option 不放
- 3.如果代码不提示，可能前面有汉字
- 4.关于控件的弱引用，为什么不被销毁，因为控件是添加到view上面到，view 上面的控件有一个数组，当控件添加到view上面，数组中也  就添加了，这是强引用，所以一般添加控件添加到view上面用的是weak 也没关系.
- 5.利用scrollview进行缩放
```objc
      UIImageView *imageView=[[UIImageView alloc]initWithImage:[UIImage imageNamed:@"default_pic"]];
    [self.scrollView addSubview:imageView];
    self.imageView=imageView;
    self.scrollView.delegate=self;
    self.scrollView.contentSize=self.imageView.image.size;
    //缩放
    self.scrollView.minimumZoomScale=0.5;
    self.scrollView.maximumZoomScale=1.5;

  #pragma mark -<UIScrollViewDelegate>
/**
 这个方法的 返回值决定了要缩放的内容(返回值只能是UIScrollView的子控件)  只要是控件都可以缩放
 */
-(UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView{
    return self.imageView;
}
-(void)scrollViewDidZoom:(UIScrollView *)scrollView{
    NSLog(@"suofanging---%f",scrollView.zoomScale);
}
```
- 6.引导页
```objc
//scrolview可以进行一些封装，但是对于引导页来说，个人觉的还是不要封装好些，很多引导页的功能需要判断进行写，封装后还得写代理上面的，本身引导页也不是很多代码
   _scrollView=[[UIScrollView alloc]initWithFrame:CGRectMake(0, 0, Main_Width, Main_Height)];
    _scrollView.delegate=self;
    //交互
    _scrollView.userInteractionEnabled=YES;
    //是否允许滚动
    _scrollView.scrollEnabled=YES;
    //总数量可以写成一个变量，方便修改
    //设置滚动范围
    _scrollView.contentSize=CGSizeMake(4*Main_Width, 0);//设置内容范围
      _scrollView.pagingEnabled = YES;//实现分页
    //是否显示竖直滚动条
    _scrollView.showsVerticalScrollIndicator = NO;
    //是否显示水平滚动条
    _scrollView.showsHorizontalScrollIndicator=NO;
    [self.view addSubview:_scrollView];
    //分页滚动效果
    //添加图片 初始化要的页面 为每一页添加数组数据——————————这里也可以写成数组根据数组的数量进行编写
    for (int i=0;i<4; i++) {
        //创建图片
        NSString * imgName=[NSString stringWithFormat:@"new_feature_%d",i+1];
        UIImage *img=[UIImage imageNamed:imgName];
        // 创建图片视图
        UIImageView*imgView=[[UIImageView alloc] initWithImage:img];
        imgView.frame=CGRectMake(Main_Width*i, 0, Main_Width, Main_Height);
        //把图片添加到图片视图中
        [_scrollView addSubview:imgView];
//这里也可以对对应对图片视图进行修改，比如说最后一页需要添加按钮，或者添加计时器一些东西都是可以的。
    }
    //分页滚动效果
    _mainPageControl = [[UIPageControl alloc] initWithFrame:CGRectMake(0, 0, Main_Width-20*2, 25)];
    _mainPageControl.center = CGPointMake(Main_Width/2, HEIGHT(self.scrollView)-120);
    //总页数
    _mainPageControl.numberOfPages =4;
    //当前页
    _mainPageControl.currentPage=0;
    _mainPageControl.currentPageIndicatorTintColor = [UIColor grayColor];
    _mainPageControl.pageIndicatorTintColor = [UIColor whiteColor];
    [self.view addSubview:self.mainPageControl];
}
//1、完成滚动：scrollViewDidScroll；
//2、将要开始拖动：scrollViewWillBeginDragging；
//3、结束拖动：scrollViewDidEndDragging
//4、滚动将要开始减速：scrollViewWillBeginDecelerating；
//5、滚动减速到停止：scrollViewDidEndDecelerating。
//6、是否滚动到顶部：scrollViewShouldScrollToTop
//7、滚动到顶部：scrollViewDidScrollToTop
-(void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    //在scrollViewDidScroll内实现监听contentOffset内容偏移量；
    int pageInt = (int)（scrollView.contentOffset.x/Main_Width＋0.5）;
    self.mainPageControl.currentPage = pageInt;
    //  if (scrollView.contentOffset.x > Main_Width*2 +100) {}这指当最后一页滑过多半然后执行某个方法
}
```
- 6.广告轮播图
   - 这里就介绍一个第三方封装好的框架，对应的方法都能实现
   - SDCycleScrollView 是一个大神封装的
- 7.滑动向上显示导航控制器／界面向上移动，变色
- 8.简单到一些定义
```objc
  //消息循环\运行循环
  runloop
  /**
 * 监听控制器view的点击.
 */
 - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    self.pageView.frame = CGRectMake(0, 0, 200, self.pageView.frame.size.height + 10);
}
//重要1.错误  CUICatalog....null
```
- 随机数

  arc4random_uniform(100) 随机数0-100
- 当frame 改变后进行动画效果的改变

  [self.view laoutIfNeeded];
