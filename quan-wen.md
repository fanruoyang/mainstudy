####1  no such file or directory
- 在 Build Settings 的 Other Linker Flags 中添加了，然后没有导入框架
- 或者在上面导入的时候位置发生变化，导致找不到路径

#### 2 architecture x86_64 错误
- 在真机上使用就行

##### 3 将URL转换成Image
UIImage *image = [UIImage imageWithData:[NSData dataWithContentsOfURL:[NSURL URLWithString:@""]]];     