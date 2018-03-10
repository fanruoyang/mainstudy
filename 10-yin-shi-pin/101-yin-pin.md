
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
