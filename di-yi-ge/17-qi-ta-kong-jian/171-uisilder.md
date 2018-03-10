
![](/assets/66C1E978-A159-4E14-9501-811E235C2D32.png)


- 移动滑块位置显示对应的数字，或者输入数字显示对应的滑块位置并改变对应状态.

```objc

-(void)slider{
//手动输入值使滑块移动过去
    importTextfied=[[UITextField alloc]initWithFrame:CGRectMake(20, 160, 100, 30)];
    importTextfied.delegate=self;
    importTextfied.backgroundColor=[UIColor grayColor];
    importTextfied.userInteractionEnabled=YES;
    [self.view addSubview:importTextfied];
   
    suretLabel=[[UILabel alloc]initWithFrame:CGRectMake(160, 160, 50, 30)];
    suretLabel.backgroundColor=[UIColor blueColor];
    suretLabel.text=[NSString stringWithFormat:@"%f",value];

    [self.view addSubview:suretLabel];
   
    UIButton *sureButton=[[UIButton alloc]initWithFrame:CGRectMake(220, 160, 50, 30)];
    sureButton.backgroundColor=[UIColor orangeColor];
    [sureButton setTitle:@"确定" forState:UIControlStateNormal];
    [sureButton addTarget:self action:@selector(sure:) forControlEvents:UIControlEventTouchUpInside];
   
    [self.view addSubview:sureButton];
   
    //建立控件 高度是默认的
    slider=[[UISlider alloc]initWithFrame:CGRectMake(30, 180, Main_Width-60, 100)];
    slider.minimumValue=0;//设置最小
    slider.maximumValue=1;//设置最大
    slider.value = 0.3;//设置默认值
    [self.view addSubview:slider];
    slider.continuous = YES;//默认YES  如果设置为NO，则每次滑块停止移动后才触发事件
    [slider addTarget:self action:@selector(sliderChange:) forControlEvents:UIControlEventValueChanged];
   
    slider.minimumTrackTintColor = [UIColor redColor];//小的颜色
    slider.maximumTrackTintColor = [UIColor yellowColor];//大的颜色
    slider.thumbTintColor = [UIColor purpleColor];//滑块的颜色
   
    slider.minimumValueImage = [UIImage imageNamed:@"card"];//小图片
    slider.maximumValueImage = [UIImage imageNamed:@"collect"];//大图片
    //自定义
//    [slider setThumbImage:[UIImage imageNamed:@"card"] forState:UIControlStateNormal];//滑块平常
//    [slider setThumbImage:[UIImage imageNamed:@"card"] forState:UIControlStateHighlighted];//滑块高亮
//    [slider setMinimumTrackImage:[UIImage imageNamed:@"card"]forState:UIControlStateNormal];//滑块左边线背景
//    [slider setMaximumTrackImage:[UIImage imageNamed:@"card"] forState:UIControlStateNormal];//滑块右边线背景
   
}
- (void)sliderChange:(id) sender {
    if ([sender isKindOfClass:[UISlider class]]) {
        slider = sender;
        value = slider.value;
        NSLog(@"%f", value);
        self.view.backgroundColor = [UIColor colorWithRed:value green:value blue:value alpha:value];
        suretLabel.text=[NSString stringWithFormat:@"%0.2f",value];
    }
}
-(void)sure:(UIButton *)sender
{
   
    NSString *text=[NSString stringWithFormat:@"%@",importTextfied.text];
   
    CGFloat a;
    a=[text floatValue];
//输入字确定位置和状态
    [slider setValue:a animated:YES];
    self.view.backgroundColor = [UIColor colorWithRed:a green:a blue:a alpha:a];
     suretLabel.text=[NSString stringWithFormat:@"%0.2f",a];
    NSLog(@"选择输入的数字%f",a);
   
}
//重新定义俩边的宽度
//- (CGRect)thumbRectForBounds:(CGRect)bounds trackRect:(CGRect)rect value:(float)value
//{
//    rect.origin.x = rect.origin.x - 10 ;
//    rect.size.width = rect.size.width +20;
//    return CGRectInset ([super thumbRectForBounds:bounds trackRect:rect value:value], 10 , 10);
//}

//-(CGRect)trackRectForBounds:(CGRect)bounds
//{
//    bounds.origin.x=15;
//    bounds.origin.y=bounds.size.height/3;
//    bounds.size.height=bounds.size.height/5;
//    bounds.size.width=bounds.size.width-30;
//    return bounds;
//}
//- (CGRect)minimumValueImageRectForBounds:(CGRect)bounds;
//- (CGRect)maximumValueImageRectForBounds:(CGRect)bounds;
```