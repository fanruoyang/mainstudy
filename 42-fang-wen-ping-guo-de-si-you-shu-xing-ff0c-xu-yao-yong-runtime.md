#### 1 runtime  获取系统手势

```objc
  // 取出系统手势的target对象，
    
    //  取出某个对象里面属性 1.KVC 前提条件：必须知道属性名 2.运行时    
    // 遍历某个类里面所有属性 Ivar:表示成员属性
    // copyIvarList只能获取哪个类下面的属性，并不会越界（不会把它的父类的属性给遍历出来）
    // Class 获取哪个类的成员属性
    // count:告诉你当前类里面成员属性的总数
//    unsigned int count = 0;
//    // 返回成员属性的数组
//    Ivar *ivars = class_copyIvarList([UIGestureRecognizer class], &count);
//    
//    for (int i = 0; i < count; i++) {
//        // 取出成员变量
//        Ivar ivar = ivars[i];
//        
//        // 获取属性名
//        NSString *ivarName = @(ivar_getName(ivar));
//        
//        NSLog(@"%@",ivarName);
//        
//        
//    }
    
    // _targets:属性名 value
```