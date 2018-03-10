
####1 录音

- 设置录制音频的质量

```
// 创建录音配置信息的字典
NSMutableDictionary *setting = [NSMutableDictionary dictionary];
// 音频格式
setting[AVFormatIDKey] = @(kAudioFormatAppleIMA4);
// 录音采样率(Hz) 如：AVSampleRateKey==8000/44100/96000（影响音频的质量）
setting[AVSampleRateKey] = @(8000.0);
// 音频通道数 1 或 2
setting[AVNumberOfChannelsKey] = @(1);
// 线性音频的位深度  8、16、24、32
setting[AVLinearPCMBitDepthKey] = @(8);
//录音的质量
setting[AVEncoderAudioQualityKey] = [NSNumber numberWithInt:AVAudioQualityHigh];

```
- 代码  LVRecordTool 封装类

```objc
#import <AVFoundation/AVFoundation.h>
@property (nonatomic, strong) AVAudioRecorder *recoder;
- (AVAudioRecorder *)recoder
{
    if (_recoder == nil) {
        // 1.创建存放录音文件的地址
        NSString *path = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) lastObject];
        NSString *filePath = [path stringByAppendingPathComponent:@"321.caf"];
        NSURL *url = [NSURL URLWithString:filePath];
        
        // 2.创建录音对象
        self.recoder = [[AVAudioRecorder alloc] initWithURL:url settings:nil error:nil];
        
        // 3.准备录音
        [self.recoder prepareToRecord];
    }
    return _recoder;
}

```

#### 2播放音效 
- 封装好
- 这里的桥接URL可能会内存泄漏
- 也可以用单例，那样就可以直接进行@por

```
#import "XMGAudioTool.h"
#import <AVFoundation/AVFoundation.h>

@implementation XMGAudioTool

static NSMutableDictionary *_soundIDs;

+ (void)initialize
{
    _soundIDs = [NSMutableDictionary dictionary];
}

//+ (NSMutableDictionary *)soundIDs
//{
//    if (_soundIDs == nil) {
//        _soundIDs = [NSMutableDictionary dictionary];
//    }
//    
//    return _soundIDs;
//}

+ (void)playSoundWithSoundname:(NSString *)soundname
{
    // 1.定义SystemSoundID
    SystemSoundID soundID = 0;
    
    // 2.从字典中取出对应soundID,如果取出是nil,表示之前没有存放在字典
    soundID = [_soundIDs[soundname] unsignedIntValue];
    if (soundID == 0) {
        CFURLRef url = (__bridge CFURLRef)[[NSBundle mainBundle] URLForResource:soundname withExtension:nil];
        AudioServicesCreateSystemSoundID(url, &soundID);
        
        // 将soundID存入字典
        [_soundIDs setObject:@(soundID) forKey:soundname];
    }
    
    // 3.播放音效
    AudioServicesPlaySystemSound(soundID);
}
```
##### 3 播放音乐
- 简单的播放，实际中会考虑到很多状况 
- 跟随上方

```
// 播放音乐 musicName : 音乐的名称
+ (void)playMusicWithMusicName:(NSString *)musicName;
// 暂停音乐 musicName : 音乐的名称
+ (void)pauseMusicWithMusicName:(NSString *)musicName;
// 停止音乐 musicName : 音乐的名称
+ (void)stopMusicWithMusicName:(NSString *)musicName;

.m
+ (void)playMusicWithMusicName:(NSString *)musicName
{
    //断言，如果为空，直接崩溃。发布的时候不用，我干了2年多也没用到过
    assert(musicName);
    
    // 1.定义播放器
    AVAudioPlayer *player = nil;
    
    // 2.从字典中取player,如果取出出来是空,则对应创建对应的播放器
    player = _players[musicName];
    if (player == nil) {
        // 2.1.获取对应音乐资源
        NSURL *fileUrl = [[NSBundle mainBundle] URLForResource:musicName withExtension:nil];
        
        if (fileUrl == nil) return;
        
        // 2.2.创建对应的播放器
        player = [[AVAudioPlayer alloc] initWithContentsOfURL:fileUrl error:nil];
        
        // 2.3.将player存入字典中
        [_players setObject:player forKey:musicName];
        
        // 2.4.准备播放
        [player prepareToPlay];
    }
    
    // 3.播放音乐
    [player play];
}

+ (void)pauseMusicWithMusicName:(NSString *)musicName
{
    assert(musicName);
    
    // 1.取出对应的播放
    AVAudioPlayer *player = _players[musicName];
    
    // 2.判断player是否nil
    if (player) {
        [player pause];
    }
}

+ (void)stopMusicWithMusicName:(NSString *)musicName
{
    assert(musicName);
    
    // 1.取出对应的播放
    AVAudioPlayer *player = _players[musicName];
    
    // 2.判断player是否nil
    if (player) {
        [player stop];
        [_players removeObjectForKey:musicName];
        player = nil;
    }
}

```


