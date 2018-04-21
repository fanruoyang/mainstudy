####    Masonry
-  mas_makeConstraints作用:给控件设置布局,把控件的所有约束保存到约束制造者中.
- ######- (NSArray *)mas_makeConstraints:(void(^)(MASConstraintMaker *))block
-   1.创建一个约束制造者
-   2.调用block(maker),把所有的控件的约束全部保存到约束制造者
-   3.[constraintMaker install]:遍历约束制造者的所有约束给控件添加约束


##### 用block将下一个界面的方法放到上一个界面使用
##### 仿masonry 加减算法
```
``` 
