#### 1 AVPlayer

[self.player play];
- 音乐
```
   self.player = [[AVPlayer alloc] initWithURL:[NSURL URLWithString:@"http://fm.baidu.com/#/songpage/245298173/假如爱有天意"]];
```

- 视频

```
    NSURL *url = [NSURL URLWithString:@"http://v1.mukewang.com/a45016f4-08d6-4277-abe6-bcfd5244c201/L.mp4"];
        
        // 2.创建AVPlayerItem
        AVPlayerItem *item = [AVPlayerItem playerItemWithURL:url];
        
        //--单独播放音乐
        
       // _player = [AVPlayer playerWithURL:url];
        
        // 3.创建AVPlayer
        _player = [AVPlayer playerWithPlayerItem:item];
        
        // 4.添加AVPlayerLayer
        AVPlayerLayer *layer = [AVPlayerLayer playerLayerWithPlayer:self.player];
        layer.frame = CGRectMake(0, 0, self.view.bounds.size.width, self.view.bounds.size.width * 9 / 16);
        [self.view.layer addSublayer:layer];
```


#### 2 MPMoviePlayerController
- 随意位置

```
- (MPMoviePlayerController *)player
{
    if (_player == nil) {
        NSURL *url = [NSURL URLWithString:@"http://v1.mukewang.com/19954d8f-e2c2-4c0a-b8c1-a4c826b5ca8b/L.mp4"];
        
        _player = [[MPMoviePlayerController alloc] initWithContentURL:url];
        
        // 设置控制器View所在的位置
        _player.view.frame = CGRectMake(0, 0, self.view.bounds.size.width, self.view.bounds.size.width * 9 / 16);
        
        // 设置播放器的控制模式
        _player.controlStyle = MPMovieControlStyleFullscreen;
        
        [self.view addSubview:self.player.view];
    }
    return _player;
}
    
```
#### 3 MPMoviePlayerViewController

- 弹出播放


```
- (MPMoviePlayerViewController *)playerVc
{
    if (_playerVc == nil) {
        NSURL *url = [NSURL URLWithString:@"http://v1.mukewang.com/19954d8f-e2c2-4c0a-b8c1-a4c826b5ca8b/L.mp4"];
        
        _playerVc = [[MPMoviePlayerViewController alloc] initWithContentURL:url];
    }
    return _playerVc;
}

- (IBAction)play {
    [self presentViewController:self.playerVc animated:YES completion:nil];
}

```
