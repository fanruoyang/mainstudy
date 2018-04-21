##### KVO的底层实现：就是判断有没有调用对象的set方法
- 1.创建NSKVONotifying，做KVO
- 2.修改当前对象的isa指针->NSKVONotifying_NSString
- 3.只要调用对象的set，就会调用NSKVONotifying_的set方法
- 4.重写NSKVONotifying_的set方法，1.[super set:]2.通知观察者，告诉你属性判断

#### 自定义实现KVO底层实现

```
```