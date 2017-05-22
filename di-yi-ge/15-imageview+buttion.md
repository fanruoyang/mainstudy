####图片拉伸
```objc
    // 拉伸处理（说明需要保护的区域）
    imageView.image = [imageView.image  resizableImageWithCapInsets:UIEdgeInsetsMake(30, 30, 30, 30) resizingMode:UIImageResizingModeStretch];
   //  image = [image stretchableImageWithLeftCapWidth:image.size.width * 0.5 topCapHeight:image.size.height * 0.5];
```
- 类方法

```objc
@implementation UIImage (Extension)
+ (UIImage *)resizableImage:(NSString *)name
{
  UIImage *image = [self imageNamed:name];
    return [image stretchableImageWithLeftCapWidth:image.size.width * 0.5 topCapHeight:image.size.height * 0.5];
}
```
####设置按钮到label和背景图片，图片布满label
- 需要在uibutton设置self.textButton.ContentInsets
 self.textButton.contentEdgeInsets= UIEdgeInsetsMake(0, 10, 71, 10);
```objc
    self.otherTextButton.titleLabel.numberOfLines = 0;
    // 设置按钮的高度就是titleLabel的高度
    [showTextButton updateConstraints:^(MASConstraintMaker *make) {
        CGFloat buttonH = showTextButton.titleLabel.frame.size.height + 30;
        make.height.equalTo(buttonH);
    }];
```

####设置文本框左边的内容
```objc
    UIView *leftView = [[UIView alloc] init];
    leftView.frame = CGRectMake(0, 0, 10, 0);
    self.messageField.leftView = leftView;
    self.messageField.leftViewMode = UITextFieldViewModeAlways;
```
