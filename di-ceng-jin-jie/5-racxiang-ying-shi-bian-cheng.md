### RAC 分成俩个版本 
- ReactiveObjc  和 swift
- 前提：已经安装了CocoaPods
1. 在Podfile写上pod 'ReactiveObjC', '~>3.0.0'
2. 更新pod依赖：进入到工程目录下，在终端输入pod update --no-repo-update 然后回车
3. 导入#import “ReactiveObjC.h”

##### 导入注意问题
- 1.如果你只是纯 swift 项目，你继续使用 ReactiveCocoa 。但是 RAC 依赖于 ReactiveSwift ，等于你引入了两个库。这种情况下的podfile的文件如下:

```
use_frameworks!
target 'Target名称' do
pod 'ReactiveCocoa', '5.0.0-alpha.3'
end
```

- 2.如果你的项目是纯 OC 项目，你需要使用的是 ReactiveObjC 。这个库里面包含原来 RAC 2 的全部代码。这种情况下的podfile的文件如下:
```
use_frameworks!
target 'Target名称' do
pod 'ReactiveObjC', '~> 2.1.0'
end
```
- 3.如果你的项目是 swift 和 OC 混编，你需要同时引用ReactiveCocoa 和 ReactiveObjCBridge 。但是 ReactiveObjCBridge 依赖于 ReactiveObjC ，所以你就等于引入了 4 个库。其中，ReactiveObjCBridge暂不支持cocoapods导入，需要手动导入！！
这种情况下的podfile的文件如下（注意，ReactiveObjCBridge手动导入就好啦）:
```
use_frameworks!
target 'Target名称' do
pod 'ReactiveObjC', '~> 2.1.0'
pod 'ReactiveCocoa', '5.0.0-alpha.3'
end
```