####  这里需要注意点就是要进行类的封装使用
/*
 问题一:默认进来没有皮肤颜色
 
 问题二:没有记录用户选中皮肤颜色
 
 问题三:和美工沟通的问题
 
 问题四:多个控制器的换肤
 
 问题五:换肤的ImageView不要写在viewDidLoad方法
 */


```
#import "SkinTool.h"

@implementation SkinTool

static NSString *_skinColor;

+ (void)initialize
{
    _skinColor = [[NSUserDefaults standardUserDefaults] objectForKey:@"skinColor"];
    if (_skinColor == nil) {
        _skinColor = @"blue";
    }
}

+ (void)setSKinColor:(NSString *)skinColor
{
    _skinColor = skinColor;
    
    // 保存用户选中的皮肤颜色
    [[NSUserDefaults standardUserDefaults] setObject:skinColor forKey:@"skinColor"];
    [[NSUserDefaults standardUserDefaults] synchronize];
}

+ (UIImage *)skinToolWithImageName:(NSString *)imageName
{
    NSString *imagePath = [NSString stringWithFormat:@"skin/%@/%@", _skinColor ,imageName];
    
    return [UIImage imageNamed:imagePath];
}

+ (UIColor *)skinToolWithLabelColor
{
    // 1.获取plist的路径
    NSString *plistName = [NSString stringWithFormat:@"skin/%@/bgColor.plist", _skinColor];
    NSString *plistPath = [[NSBundle mainBundle] pathForResource:plistName ofType:nil];
    
    // 2.读取颜色的点击
    NSDictionary *colorDict = [NSDictionary dictionaryWithContentsOfFile:plistPath];
    
    // 3.读取对应颜色的字符串
    NSString *colorString = colorDict[@"labelBgColor"];
    
    // 4.获取颜色数组
    NSArray *colorArray = [colorString componentsSeparatedByString:@","];
    
    // 5.读取对应RGB
    NSInteger red = [colorArray[0] integerValue];
    NSInteger green = [colorArray[1] integerValue];
    NSInteger blue = [colorArray[2] integerValue];
    
    return [UIColor colorWithRed:red/255.0 green:green/255.0 blue:blue/255.0 alpha:1.0];
}

```