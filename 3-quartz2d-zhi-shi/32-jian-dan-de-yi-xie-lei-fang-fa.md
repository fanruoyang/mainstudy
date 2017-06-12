#### 1.封装 裁剪个人头像圆形
    UIImage *image = [UIImage imageWithClipImage:[UIImage imageNamed:@"阿狸头像"] borderWidth:1 borderColor:[UIColor redColor]];
    
    _imageView.image = image;
    
```
#import "UIImage+Image.h"

@implementation UIImage (Image)
+ (UIImage *)imageWithClipImage:(UIImage *)image borderWidth:(CGFloat)borderWidth borderColor:(UIColor *)color
{
    // 图片的宽度和高度
    CGFloat imageWH = image.size.width;
    // 设置圆环的宽度
    CGFloat border = borderWidth;
    // 圆形的宽度和高度
    CGFloat ovalWH = imageWH + 2 * border;
    // 1.开启上下文
    UIGraphicsBeginImageContextWithOptions(CGSizeMake(ovalWH, ovalWH), NO, 0);
    // 2.画大圆
    UIBezierPath *path = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(0, 0, ovalWH, ovalWH)];
    [color set];
    [path fill];
    // 3.设置裁剪区域
    UIBezierPath *clipPath = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(border, border, imageWH, imageWH)];
    [clipPath addClip];
    // 4.绘制图片
    [image drawAtPoint:CGPointMake(border, border)];
    // 5.获取图片
    UIImage *clipImage = UIGraphicsGetImageFromCurrentImageContext();
    // 6.关闭上下文
    UIGraphicsEndImageContext();
    return clipImage;
    
}

@end

+ (UIImage *)imageWithClipImage:(UIImage *)image borderWidth:(CGFloat)borderWidth borderColor:(UIColor *)color;
```

#### 2.封装 控件截屏生成图片
     UIImage *image =  [UIImage imageWithCaputureView:self.view];
    
    // image转data
    // compressionQuality： 图片质量 1:最高质量
    
     NSData *data = UIImageJPEGRepresentation(image,1);
    
    [data writeToFile:@"/Users/xiaomage/Desktop/view.png" atomically:YES];
    
+ (UIImage *)imageWithCaputureView:(UIView *)view;

```
+ (UIImage *)imageWithCaputureView:(UIView *)view
{
    // 开启位图上下文
    UIGraphicsBeginImageContextWithOptions(view.bounds.size, NO, 0);
    
    // 获取上下文
    CGContextRef ctx = UIGraphicsGetCurrentContext();
    
    // 把控件上的图层渲染到上下文,layer只能渲染
    [view.layer renderInContext:ctx];
    
    // 生成一张图片
    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
    
    // 关闭上下文
    UIGraphicsEndImageContext();
    
    return image;
}
```


#### 3.图片直接截取

```
@property (nonatomic, assign) CGPoint startP;

@property (weak, nonatomic) IBOutlet UIImageView *imageV;
@property (nonatomic, weak) UIView *clipView;

@end

@implementation ViewController

- (UIView *)clipView{
    if (_clipView == nil) {
        UIView *view = [[UIView alloc] init];
        _clipView = view;
        view.backgroundColor = [UIColor blackColor];
        view.alpha = 0.5;
        [self.view addSubview:view];
    }
    return _clipView;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    // 给控制器的view添加一个pan手势
    UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(pan:)];
    [self.view addGestureRecognizer:pan];
}

- (void)pan:(UIPanGestureRecognizer *)pan
{
    CGPoint endA = CGPointZero;
    
    if (pan.state == UIGestureRecognizerStateBegan) { // 一开始拖动的时候
        // 获取一开始触摸点
      _startP = [pan locationInView:self.view];
        
    }else if(pan.state == UIGestureRecognizerStateChanged){ // 一直拖动
        // 获取结束点
         endA = [pan locationInView:self.view];
        
        CGFloat w = endA.x - _startP.x;
        CGFloat h = endA.y - _startP.y;
        
        // 获取截取范围
        CGRect clipRect = CGRectMake(_startP.x, _startP.y, w, h);

        
        // 生成截屏的view
        self.clipView.frame = clipRect;
        
        
        
    }else if (pan.state == UIGestureRecognizerStateEnded){     
        // 图片裁剪，生成一张新的图pian   
        // 开启上下文
        // 如果不透明，默认超出裁剪区域会变成黑色，通常都是透明
        UIGraphicsBeginImageContextWithOptions(_imageV.bounds.size, NO, 0);   
        // 设置裁剪区域
       UIBezierPath *path =  [UIBezierPath bezierPathWithRect:_clipView.frame];    
        [path addClip];    
        // 获取上下文
        CGContextRef ctx = UIGraphicsGetCurrentContext();        
        // 把控件上的内容渲染到上下文
        [_imageV.layer renderInContext:ctx];    
        // 生成一张新的图片
        _imageV.image = UIGraphicsGetImageFromCurrentImageContext();      
        // 关闭上下文
        UIGraphicsEndImageContext();      
        // 先移除
        [_clipView removeFromSuperview];
        // 截取的view设置为nil
        _clipView = nil;
        
    }
    // 获取手指的偏移量
//    pan translationInView:<#(UIView *)#>
}

```