####1  no such file or directory
- 在 Build Settings 的 Other Linker Flags 中添加了，然后没有导入框架
- 或者在上面导入的时候位置发生变化，导致找不到路径

#### 2 architecture x86_64 错误
- 在真机上使用就行

#### 3 将URL转换成Image
- UIImage *image = [UIImage imageWithData:[NSData dataWithContentsOfURL:[NSURL URLWithString:@""]]];     

#### 4 block 的循环，如果在block 中引用当前的类，报警告循环引用
- Capturing 'view' strongly in this block is likely to lead to a retain cycle

```
  FH_GodChargeView *view = [[FH_GodChargeView alloc] init];
    __block FH_GodChargeView *blockSelf = view;
    view.chargeSuccessBlock = ^{
       [blockSelf backAction];
    };
```
#### 5 wkwebview goback上移64像素的问题
-** 这行要写在frame 的后面才可以  ** 弄了半天，太坑了

```
       
        if (@available(iOS 11.0, *)) {
            _myWebView.scrollView.contentInsetAdjustmentBehavior = UIScrollViewContentInsetAdjustmentNever;

        } else {
            self.automaticallyAdjustsScrollViewInsets = NO;
        }
        
```