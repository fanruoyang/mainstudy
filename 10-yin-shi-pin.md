#### 一、音频播放
##### 1.音效播放（短时间的音频文件）
-  AudioServicesCreateSystemSoundID
-  AudioServicesPlaySystemSound

##### 2.音乐播放（长时间的音频文件）
-  AVAudioPlayer
**只能播放本地的音频文件**
- MPMusicPlayerController


##### 3. AVPlayer
能播放本地、远程的音频、视频文件
基于Layer显示，得自己去编写控制面板

##### 4. MPMoviePlayerController
能播放本地、远程的音频、视频文件
自带播放控制面板（暂停、播放、播放进度、是否要全屏）

##### 5. MPMoviePlayerViewController
能播放本地、远程的音频、视频文件
内部是封装了MPMoviePlayerController
播放界面默认就是全屏的
如果播放功能比较简单，仅仅是简单地播放远程、本地的视频文件，建议用这个

##### 6. DOUAudioStreamer
能播放远程、本地的音频文件
监听缓冲进度、下载速度、下载进度

#### 二、视频播放
- 1.音乐播放中2> 3> 4>

- 2.VLC

- 3.FFmpeg
- kxmovie
- Vitamio

三、录音
1.AVAudioRecorder