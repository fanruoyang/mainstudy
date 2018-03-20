#### iPhone内置的传感器有
- 运动传感器\加速度传感器\加速计（Motion/Accelerometer Sensor）
  - 加速计可以用于检测设备的摇晃，经典应用场景
  - 摇一摇
  - 计步器
 
- 环境光传感器（Ambient Light Sensor）
   - 当你使用iPhone拍照时，闪光灯会在一定条件下自动开启

- 距离传感器（Proximity Sensor）
  - 用于检测是否有其他物体靠近设备屏幕
  
```
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    // [UIApplication sharedApplication].proximitySensingEnabled = YES;
    [UIDevice currentDevice].proximityMonitoringEnabled = YES;
    
    // 监听有物品靠近还是离开
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(proximityStateDidChange) name:UIDeviceProximityStateDidChangeNotification object:nil];
}

- (void)proximityStateDidChange
{
    if ([UIDevice currentDevice].proximityState) {
        NSLog(@"有物品靠近");
    } else {
        NSLog(@"有物品离开");
    }
}

```

- 磁力计传感器（Magnetometer Sensor）
 - 可以感应地球磁场， 获得方向信息， 使位置服务数据更精准
 - 可以用于电子罗盘和导航应用
 - iPad的Smart Cover盒盖睡眠操作就是基于磁力计传感器

- 内部温度传感器（Internal Temperature Sensor）
  - 内部温度传感器，对于提升iOS设备自身安全性与稳定性有很大的帮助 

- 湿度传感器（Moisture Sensor）
  - Apple的维修人员就是通过检测试纸是否变红，来判断设备是否进水
（设备进水不在保修范围之内）

- 陀螺仪（Gyroscope）
 - 游戏 角速度

