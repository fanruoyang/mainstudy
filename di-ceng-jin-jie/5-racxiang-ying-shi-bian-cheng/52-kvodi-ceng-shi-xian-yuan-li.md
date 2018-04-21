##### KVO的底层实现：就是判断有没有调用对象的set方法
- 1.创建NSKVONotifying，做KVO
- 2.修改当前对象的isa指针->NSKVONotifying_NSString
- 3.只要调用对象的set，就会调用NSKVONotifying_的set方法
- 4.重写NSKVONotifying_的set方法，1.[super set:]2.通知观察者，告诉你属性判断
- 5.合理运用runtime

#### 自定义实现KVO底层实现

```
-(void)fry_addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(void *)context{
    //1.动态创建NSKVONotifying_Persong,NSKVONotifying_Person是person子类。做KVO
    //2.修改当前对象的isa指针-->NSKVONotifying_Person
    //3.只要调用对象的set方法，就会调用NSKVONotifying_Person的set方法
    //4.重写NSKVONotifying_Person的set方法，1[super set:] 2.通知观察者，告诉你的属性改变
    //5.修改isa，本质就是修改当前对象的类名
    
    object_setClass(self, [FYRGKVONotifying_Person class]);
    
    //观察者保存到对象中
    
    //添加关联
    //id object:给哪个对象添加属性
    //key:属性名
    //value：关联值
    objc_setAssociatedObject(self, @"observer", observer, OBJC_ASSOCIATION_COPY_NONATOMIC);
    
}




#import "FYRGKVONotifying_Person.h"
#import <objc/runtime.h>

@implementation FYRGKVONotifying_Person
-(void)setAge:(int)age{
    [super setAge:age];
    //调用KVO
    //获取观察者
    id observer=objc_getAssociatedObject(self, @"observer");
    
    //调用观察者
    
    [observer observeValueForKeyPath:@"age" ofObject:observer change:nil context:nil];
}

```