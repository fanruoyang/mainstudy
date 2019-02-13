####  MVP  模式 面向协议 直播架构
- 1  


#### 组件划分
  - 基础组件(宏，常量) 分类， 网络，工具类
  - 功能组件  控件 功能
  - 业务组件 通信 1 URL +protocol  2 Runtime + target-Action
  
#### 制作pod库
- 1 本地pod库
   - pod lib create xxx
   - 把相应代码集成到项目中
   - 主工程执行 pod 'xxx',:path'本地路径'  
   - 如果有依赖库，在podspec 文件中 s.dependency '第三方库','~>2.3'
   
- 2 制作远程库
     
   
