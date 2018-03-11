####  1 修改APP的 状态栏颜色
- 1 单个控制器修改

```
- (UIStatusBarStyle)preferredStatusBarStyle
{
    return UIStatusBarStyleLightContent;
}
```
####  2 毛玻璃效果

- 1 美工
- 2 第三方框架
- 3 CoreImage：图片的高斯模糊
- 4 UIToolBar

```
    UIToolbar *toolBar = [[UIToolbar alloc] init];
    [toolBar setBarStyle:UIBarStyleBlack];
    [self.albumView addSubview:toolBar];
    toolBar.translatesAutoresizingMaskIntoConstraints = NO;
```
#### 3 播放封装
-.h
```
#import <Foundation/Foundation.h>
@class XMGMusic;

@interface XMGMusicTool : NSObject

+ (NSArray *)musics;

+ (XMGMusic *)playingMusic;
//设置播放的歌曲
+ (void)setPlayingMusic:(XMGMusic *)playingMusic;

+ (XMGMusic *)nextMusic;

+ (XMGMusic *)previousMusic;

@end

```

- .m

```objc
#import "XMGMusic.h"
#import "MJExtension.h"

@implementation XMGMusicTool

static NSArray *_musics;
static XMGMusic *_playingMusic;

+ (void)initialize
{
    if (_musics == nil) {
        _musics = [XMGMusic objectArrayWithFilename:@"Musics.plist"];
    }
    
    if (_playingMusic == nil) {
        _playingMusic = _musics[1];
    }
}

+ (NSArray *)musics
{
    return _musics;
}

+ (XMGMusic *)playingMusic
{
    return _playingMusic;
}

+ (void)setPlayingMusic:(XMGMusic *)playingMusic
{
    _playingMusic = playingMusic;
}

+ (XMGMusic *)nextMusic
{
    // 1.拿到当前播放歌词下标值
    NSInteger currentIndex = [_musics indexOfObject:_playingMusic];
    
    // 2.取出下一首
    NSInteger nextIndex = ++currentIndex;
    if (nextIndex >= _musics.count) {
        nextIndex = 0;
    }
    XMGMusic *nextMusic = _musics[nextIndex];
    
    return nextMusic;
}

+ (XMGMusic *)previousMusic
{
    // 1.拿到当前播放歌词下标值
    NSInteger currentIndex = [_musics indexOfObject:_playingMusic];
    
    // 2.取出下一首
    NSInteger previousIndex = --currentIndex;
    if (previousIndex < 0) {
        previousIndex = _musics.count - 1;
    }
    XMGMusic *previousMusic = _musics[previousIndex];
    
    return previousMusic;
}


```