##### 1 静态库和动态库的存在形式

- 静态库: .a和. framework
- 动态库: .dylib 和.framework
- 编译 comment +B

##### 2动态库和静态库的区别

 - 链接时，静态库会被完整地复制到可执行文件中，被多次使用就有多份冗余拷贝
 - 动态库：链接时不复制，程序运行时由系统动态加载到内存，供程序调用，系统只加载一次，多个程序共用，节省内存（右图所示）
 
**注意:项目中如果使用了自制的动态库，不能被上传到AppStore**

##### 3 开发.a
-  选择 Cocoa Touch Static Library 建立
-  在Build Phases 中将.h文件暴露出来
![](/assets/DDB61CED-E789-4283-BBE4-4F8A199DAC1A.png)

-  运行编译后就能得到对应的.a静态库
-  编译静态库后，使用不同的机型是报错，因为CPU架构不同，合并
#####这里变成NO的话是编译全部的静态库
![](/assets/41348F9C-3BB4-467B-97F4-AF944D3AF77C.png)

```
/*
 每一个设备都有属于自己的CPU架构(4s/6plus)
 每一个静态支持的架构是固定(liblibstatic.a)
 查看静态库支持的架构:lipo -info liblibstatic.a
 
 静态库合并:
 lipo -create 静态库1 静态库2 -output 新的静态库
 
 模拟器
 4s-->5 : i386
 5s-->6plus : x86_64
 
 真机
 3gs-->4s : armv7
 5/5c : armv7s,静态库只要支持了armv7,就可以跑在armv7s的架构上
 5s-->6plus : arm64
 */
``` 
-  最后给别人使用的是realese 版本的

```
静态库文件的版本(4种)
1.真机-Debug版本
2.真机-Release版本
3.模拟器-Debug版本
4.模拟器-Release版本

调试版本 VS 发布版本
--------------------------------------------------------------------------------
- 调试版本会包含完整的符号信息，以方便调试
- 调试版本不会对代码进行优化

- 发布版本不会包含完整的符号信息
- 发布版本的执行代码是进行过优化的
- 发布版本的大小会比调试版本的略小
- 在执行速度方面，发布版本会更快些，但不意味着会有显著的提升
```  

##### 4 开发.a 在工程文件中进行编译
-  检测.a的类型
$ lipo -info libCZTools.a

-  合并.a
 lipo -create Debug-iphoneos/libTools.a Debug-- - iphonesimulator/libTools.a -output libTools.a

-  合并.a的好处，开发过程中既可以在真机上调试，也可以在模拟器上调试
-  合并.a的坏处，如果静态库太大，合并打包后，会非常大，因此很多第三方的静态库的.a是区分版本的
- 今后在使用.a时一定注意版本
- 给别人的静态库是release的版本


##### 5 framework的使用，编译

- 1 直接在Generl中 添加，然后进行编译
![](/assets/964CBF9A-4C6C-4778-B22F-B94646B1465C.png)
1.1或者在新项目中直接建立。在拉入。无法实际测试
![](/assets/FFEEA448-F2BE-491C-A3AE-0AE1E00D3452.png)
**- 2 编译的时候记住realse版本，然后合并**
- 3 building setting 中build Active 中变为NO，适应版本
- 4 注意是静态库还是动态库，动态库使用在Generl embeded中添加、无法上架
- 5 **编译为静态库的，需要在building setting中Mach 中修改为Static Library**
![**](/assets/390C8635-9CD9-4995-8EE1-104199CDADBD.png)

##### 6 MRC的文件打包静态库可以在ARC环境下使用，现在几乎没用了

##### 7 swift 打包动态库 不支持静态库  cocopods中的use_frameworks!
- 1  swift 的代码需要添加公共方法 public
![](/assets/9429C6D6-7B00-45F5-9110-71F7BC6AB532.png)
- 2 其他都一样跟OC 



