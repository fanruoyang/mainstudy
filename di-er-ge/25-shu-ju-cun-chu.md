### 1.沙盒存储 pilst 归档

- plist 存储一般来说都是读写俩个操作

- 写入
```
    // Plist注意：不能存储自定义对象
    // Plist：数组和字典,
    // 如何判断一个对象能不能使用Plist，就看下有没有writeToFile
    NSArray *arr = @[@"123",@1];  
    // 获取应用的文件夹（应用沙盒）
    // NSString *homePath = NSHomeDirectory(); 
    // 获取temp
    // NSTemporaryDirectory();  
    // 获取Cache文件路径
    // NSSearchPathDirectory:搜索的目录
    // NSSearchPathDomainMask：搜索范围 NSUserDomainMask:表示在用户的手机上查找
    // expandTilde 是否展开全路径，如果没有展开，应用的沙盒路径就是~
    // 存储一定要要展开路径
    NSString *cachePaht = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES)[0];
    // 拼接文件名
    NSString *filePath = [cachePaht stringByAppendingPathComponent:@"personArr.plist"];
    // File:文件的全路径
    [arr writeToFile:filePath atomically:YES];

```
