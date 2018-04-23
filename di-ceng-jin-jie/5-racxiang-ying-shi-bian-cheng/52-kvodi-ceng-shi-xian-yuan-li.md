##### KVO的底层实现：就是判断有没有调用对象的set方法
- 1.创建NSKVONotifying，做KVO
- 2.修改当前对象的isa指针->NSKVONotifying_NSString
- 3.只要调用对象的set，就会调用NSKVONotifying_的set方法
- 4.重写NSKVONotifying_的set方法，1.[super set:]2.通知观察者，告诉你属性判断
- 5.合理运用runtime

####  1 自定义实现KVO底层实现

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

#### 2 OC关联对象小结---详细描述上面
- 为KVO创建一个关联的观察者

```
void objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy);
id objc_getAssociatedObject(id object, const void *key);
void objc_removeAssociatedObjects(id object);

```
**objc_setAssociatedObject**用于给对象添加关联对象，传nil可以移除相关的关联对象。
**objc_getAssocicatedObject**用于获取关联对象的值。
**objc_removeAssociatedObjec**t用于移除该对象的所有关联对象。如果打算只移除一部分则不能使用该方法。

- 相关参数
key：要保证全局唯一，key与关联的对象是一一对应关系。必须全局唯一。通常用@selector(methodName)作为key。
value：要关联的对象。
policy：关联策略。有五种关联策略。

**OBJC_ASSOCIATION_ASSIGN** 等价于 @property(assign)。
**OBJC_ASSOCIATION_RETAIN_NONATOMIC**等价于 @property(strong, nonatomic)。
**OBJC_ASSOCIATION_COPY_NONATOMIC**等价于@property(copy, nonatomic)。
**OBJC_ASSOCIATION_RETAIN**等价于@property(strong,atomic)。
**OBJC_ASSOCIATION_COPY**等价于@property(copy, atomic)。

#### 3 原理简介

运行时通过map维系一张关联对象与被关联对象之间的关系。
objc_setAssociatedObject的相关代码.
