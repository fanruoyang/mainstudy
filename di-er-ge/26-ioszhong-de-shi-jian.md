###1.UIRsponder 

- UIResponder内部提供了以下方法来处理事件
  - 触摸事件
 
    ```
    - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;
    - (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;
    - (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event;
    - (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;

    ```
 - 加速器事件
    ```
    - (void)motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event;
    - (void)motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event;
    - (void)motionCancelled:(UIEventSubtype)motion withEvent:(UIEvent *)event;
    ```
 - 远程控制事件
 
      -(void)remoteControlReceivedWithEvent:(UIEvent *)event;
      
#### 1.UIView的点击拖拽事件

   ```
     // 当手指开始触摸view
// NSArray,字典，NSSet(无序)
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    NSLog(@"%ld", touches.count);
    NSLog(@"%s",__func__);
}

// 当手指在view上移动的时候
- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event
{
    NSLog(@"%s",__func__);
    
    // 获取UITouch对象
    UITouch *touch = [touches anyObject];
    
    // 获取当前点
    CGPoint curP = [touch locationInView:self];
    
    // 获取上一个点
    CGPoint preP = [touch previousLocationInView:self];
    
    // 获取x轴偏移量
    CGFloat offsetX = curP.x - preP.x;
    
    // 获取y轴偏移量
    CGFloat offsetY = curP.y - preP.y;
    
    // 修改view的位置（frame,center,transform）
    self.transform = CGAffineTransformTranslate(self.transform, offsetX, offsetY);
   
//    self.transform = CGAffineTransformMakeTranslation(offsetX, 0);
    
}

// 当手指离开这个view的时候
//- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event{
//     NSLog(@"%s",__func__);
//}

// 当触摸事件被打断的时候调用(电话打入)
- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
{
    
}
   ```