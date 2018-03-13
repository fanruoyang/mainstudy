#### 1 播放封装
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

##### 2 开始动画  核心动画旋转VIEW

```
- (void)startIconViewAnimate
{
    // 1.创建基本动画
    CABasicAnimation *rotateAnim = [CABasicAnimation animationWithKeyPath:@"transform.rotation.z"];
    
    // 2.设置基本动画属性
    rotateAnim.fromValue = @(0);
    rotateAnim.toValue = @(M_PI * 2);
    rotateAnim.repeatCount = NSIntegerMax;
    rotateAnim.duration = 40;
    
    // 3.添加动画到图层上
    [self.iconView.layer addAnimation:rotateAnim forKey:nil];
}

```

- 暂停核心动画

```
#import "NSString+XMGTimeExtension.h"

.h
#import <QuartzCore/QuartzCore.h>

@interface CALayer (PauseAimate)

// 暂停动画
- (void)pauseAnimate;

// 恢复动画
- (void)resumeAnimate;

@end

- .m

#import "CALayer+PauseAimate.h"

@implementation CALayer (PauseAimate)

- (void)pauseAnimate
{
    CFTimeInterval pausedTime = [self convertTime:CACurrentMediaTime() fromLayer:nil];
    self.speed = 0.0;
    self.timeOffset = pausedTime;
}

- (void)resumeAnimate
{
    CFTimeInterval pausedTime = [self timeOffset];
    self.speed = 1.0;
    self.timeOffset = 0.0;
    self.beginTime = 0.0;
    CFTimeInterval timeSincePause = [self convertTime:CACurrentMediaTime() fromLayer:nil] - pausedTime;
    self.beginTime = timeSincePause;
}

@end

```

##### 3 