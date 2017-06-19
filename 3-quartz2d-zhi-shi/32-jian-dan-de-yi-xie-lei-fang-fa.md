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

#### 4.图片擦除，是2个图片，具体看DEMO 有点问题

#### 5.手势滑动密码
```

@property (nonatomic, strong) NSMutableArray *selectedsBtn;

@property (nonatomic, assign) CGPoint curP;

@end

@implementation LockView

- (NSMutableArray *)selectedsBtn
{
    if (_selectedsBtn == nil) {
        _selectedsBtn = [NSMutableArray array];
    }
    
    return _selectedsBtn;
}

- (IBAction)pan:(UIPanGestureRecognizer *)pan
{
    // 获取触摸点
    _curP = [pan locationInView:self];
    
    // 判断触摸点在不在按钮上
    for (UIButton *btn in self.subviews) {
        // 点在不在某个范围内,并且按钮没有被选中
        if (CGRectContainsPoint(btn.frame, _curP) && btn.selected == NO) {
            // 点在按钮上
            btn.selected = YES;
            
            // 保存到数组中
            [self.selectedsBtn addObject:btn];
            
        }
        
    }
    
    // 重绘
    [self setNeedsDisplay];
    
    
    if (pan.state == UIGestureRecognizerStateEnded) {
        
        // 创建可变字符串
        NSMutableString *strM = [NSMutableString string];
         // 保存输入密码
        for (UIButton *btn in self.selectedsBtn) {
            
            [strM appendFormat:@"%ld",btn.tag];
            
        }
        NSLog(@"%@",strM);
        
        // 还原界面
        
        [self.selectedsBtn enumerateObjectsUsingBlock:^(UIButton *btn , NSUInteger idx, BOOL * _Nonnull stop) {
            btn.selected=NO;

        }];
        
//        NSMutableString *str = [NSMutableString string];
//        for (UIButton *btn in self.selectedsBtn) {
//            [str appendFormat:@"%ld",btn.tag];
//            btn.selected = NO;
//        }
        // 清除画线,把选中按钮清空
        [self.selectedsBtn removeAllObjects];
      
        
    }
    
}
// 加载完xib的时候调用
- (void)awakeFromNib
{
    [super awakeFromNib];
    
    // 创建9个按钮
    for ( int i = 0; i < 9; i++) {
        UIButton *btn = [UIButton buttonWithType:UIButtonTypeCustom];
        
        // 不允许用户交互，按钮就不能点击，也就不能达到高亮状态
        btn.userInteractionEnabled = NO;
        [btn setImage:[UIImage imageNamed:@"gesture_node_normal"] forState:UIControlStateNormal];
        [btn setImage:[UIImage imageNamed:@"gesture_node_highlighted"] forState:UIControlStateSelected];
        btn.tag = i; 
        [self addSubview:btn];
    }
}


// 为什么要在这个方法布局子控件，因为只要一调用这个方法，就表示父控件的尺寸确定
- (void)layoutSubviews
{
    [super layoutSubviews];
    
    NSUInteger count = self.subviews.count;
    int cols = 3;
    
    CGFloat x = 0;
    CGFloat y = 0;
    CGFloat w = 74;
    CGFloat h = 74;
    CGFloat margin = (self.bounds.size.width - cols * w) / (cols + 1);
    
    CGFloat col = 0;
    CGFloat row = 0;
    for (NSUInteger i = 0; i < count; i++) {
        UIButton *btn = self.subviews[i];
        // 获取当前按钮的列数
        col = i % cols;
        row = i / cols;
        x = margin + col * (margin + w);
        y = row * (margin + w);   
        btn.frame = CGRectMake(x, y, w, h);    
    } 
}

// 只要调用这个方法，就会把之前绘制的东西全部清掉，重新绘制
- (void)drawRect:(CGRect)rect
{
    // 没有选中按钮，不需要连线
    if (self.selectedsBtn.count == 0) return; 
    // 把所有选中按钮中心点连线
    UIBezierPath *path = [UIBezierPath bezierPath] 
    NSUInteger count = self.selectedsBtn.count;
    // 把所有选中按钮之间都连好线
    for (int i = 0; i < count; i++) {
        UIButton *btn = self.selectedsBtn[i];
        if (i == 0) {
            // 设置起点
            [path moveToPoint:btn.center];
        }else{
            [path addLineToPoint:btn.center];
        }     
    }
    
    // 连线到手指的触摸点
    [path addLineToPoint:_curP];
    [[UIColor greenColor] set];
    path.lineWidth = 10;
    path.lineJoinStyle = kCGLineJoinRound;
    [path stroke];
    
    
}

```




