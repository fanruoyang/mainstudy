#### iPhone内置的传感器有
- 运动传感器\加速度传感器\加速计（Motion/Accelerometer Sensor）
  - 加速计可以用于检测设备的摇晃，经典应用场景
  - 摇一摇
  - 计步器
  - 一个push 一个pull
  
```
#pragma mark - 获取加速计信息
- (void)pullAccelerometer
{
    // pull
    // 1.判断加速计是否可用
    if (!self.mgr.isAccelerometerAvailable) {
        NSLog(@"加速计不可用");
        return;
    }
    
    // 2.开始采样
    [self.mgr startAccelerometerUpdates];
}

- (void)pushAccelerometer
{
    // push
    // 1.判断加速计是否可用
    if (!self.mgr.isAccelerometerAvailable) {
        NSLog(@"加速计不可用");
        return;
    }
    
    // 2.设置采样间隔
    self.mgr.accelerometerUpdateInterval = 0.3;
    
    // 3.开始采样
    [self.mgr startAccelerometerUpdatesToQueue:[NSOperationQueue mainQueue] withHandler:^(CMAccelerometerData *accelerometerData, NSError *error) { // 当采样到加速计信息时就会执行
        if (error) return;
        
        // 获取加速计信息
        CMAcceleration acceleration = accelerometerData.acceleration;
        NSLog(@"x:%f y:%f z:%f", acceleration.x, acceleration.y, acceleration.z);
    }];
}

```  
####  模拟摇一摇，其实就是监听手势

```
- (void)motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
    NSLog(@"用户摇一摇");
}

- (void)motionCancelled:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
    // 摇一摇被打断(电话)
}

- (void)motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
    // 摇一摇结束
}
```  

####  计步器
- https://www.jianshu.com/p/8f896172fb3d

```
#import <CoreMotion/CoreMotion.h>

@interface ViewController ()

/** 计步器对象 */
@property (nonatomic, strong) CMStepCounter *counter;
@property (weak, nonatomic) IBOutlet UILabel *stepLabel;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 1.判断计步器是否可用
    if (![CMStepCounter isStepCountingAvailable]) {
        NSLog(@"计步器不可用");
        return;
    }
    
    // 2.开始计步
    [self.counter startStepCountingUpdatesToQueue:[NSOperationQueue mainQueue] updateOn:5 withHandler:^(NSInteger numberOfSteps, NSDate *timestamp, NSError *error) {
        if (error) return;
        
        self.stepLabel.text = [NSString stringWithFormat:@"您一共走了%ld步", numberOfSteps];
    }];
}

#pragma mark - 懒加载代码
- (CMStepCounter *)counter
{
    if (_counter == nil) {
        _counter = [[CMStepCounter alloc] init];
    }
    return _counter;
}

```

 
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

```
    // 获取磁力计传感器的值
    // 1.判断磁力计是否可用
    if (!self.mgr.isMagnetometerAvailable) {
        return;
    }
    
    // 2.设置采样间隔
    self.mgr.magnetometerUpdateInterval = 0.3;
    
    // 3.开始采样
    [self.mgr startMagnetometerUpdatesToQueue:[NSOperationQueue mainQueue] withHandler:^(CMMagnetometerData *magnetometerData, NSError *error) {
        if (error) return;
        
        CMMagneticField field = magnetometerData.magneticField;
        NSLog(@"x:%f y:%f z:%f", field.x, field.y, field.z);
    }];
```
- 内部温度传感器（Internal Temperature Sensor）
  - 内部温度传感器，对于提升iOS设备自身安全性与稳定性有很大的帮助 

- 湿度传感器（Moisture Sensor）
  - Apple的维修人员就是通过检测试纸是否变红，来判断设备是否进水
（设备进水不在保修范围之内）

- 陀螺仪（Gyroscope）
 - 游戏 角速度
 
```
#pragma mark - 获取陀螺仪信息
- (void)pullGyro
{
    // pull
    // 1.判断陀螺仪是否可用
    if (![self.mgr isGyroAvailable]) {
        NSLog(@"手机该换了");
        return;
    }
    
    // 2.开始采样
    [self.mgr startGyroUpdates];
}

- (void)pushGyro
{
    // push
    // 1.判断陀螺仪是否可用
    if (![self.mgr isGyroAvailable]) {
        NSLog(@"手机该换了");
        return;
    }
    
    // 2.设置采样间隔
    self.mgr.gyroUpdateInterval = 0.3;
    
    // 3.开始采样
    [self.mgr startGyroUpdatesToQueue:[NSOperationQueue mainQueue] withHandler:^(CMGyroData *gyroData, NSError *error) {
        if (error) return;
        
        CMRotationRate rate = gyroData.rotationRate;
        NSLog(@"x:%f y:%f z:%f", rate.x, rate.y, rate.z);
    }];
}

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    // 1.获取加速计信息
    CMAcceleration acceleration = self.mgr.accelerometerData.acceleration;
    NSLog(@"x:%f y:%f z:%f", acceleration.x, acceleration.y, acceleration.z);
    
    // 2.获取陀螺仪信息
    CMRotationRate rate = self.mgr.gyroData.rotationRate;
    NSLog(@"x:%f y:%f z:%f", rate.x, rate.y, rate.z);
}
  
  ```

