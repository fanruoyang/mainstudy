#### 1 应用间跳转
#### 2 分享
#### 3 本地推送
#### 4 远程推送 
#### 5 获取通讯录
#### 6 换肤
#### 7 传感器
#### 8 硬件信息获取-- UIDevice--Hardware
- 去系统的设置的文件中修改，有一个封装类方法

```
    // 1.获取手机型号
    NSLog(@"手机型号:%@", [[UIDevice currentDevice] platformString]);
    
    // 2.获取剩余空间和一共多少空间
    NSLog(@"%@--%@", [[UIDevice currentDevice] freeDiskSpace], [[UIDevice currentDevice] totalDiskSpace]);
```

#### 9 重力碰撞 UIDynamic
- 动力碰撞等一些关于动力，铰链连接，碰撞，悬挂等效果
- 物理学方面的一些动画效果
- 会飞的小鸟，等一些小游戏动画


