#### 学习知识

######1.自定义图片的分类
```objc
// 快速的返回一个最原始的图片
+ (instancetype)imageWithOriRenderingImage:(NSString *)imageName;


#import "UIImage+Image.h"

@implementation UIImage (Image)
+ (instancetype)imageWithOriRenderingImage:(NSString *)imageName
{
    UIImage *image = [UIImage imageNamed:imageName];
    return [image imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];
}
```