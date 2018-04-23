 #### 1.ReactiveCocoa作用

在我们iOS开发过程中，经常会响应某些事件来处理某些业务逻辑，例如按钮的点击，上下拉刷新，网络请求，属性的变化（通过KVO）或者用户位置的变化（通过CoreLocation）。但是这些事件都用不同的方式来处理，比如action、delegate、KVO、callback等。

其实这些事件，都可以通过RAC处理，ReactiveCocoa为事件提供了很多处理方法，而且利用RAC处理事件很方便，可以把要处理的事情，和监听的事情的代码放在一起，这样非常方便我们管理，就不需要跳到对应的方法里。非常符合我们开发中`高聚合，低耦合`的思想。
#### 2.编程思想

在开发中我们也不能太依赖于某个框架，否则这个框架不更新了，导致项目后期没办法维护，比如之前Facebook提供的`Three20框架`，在当时也是神器，但是后来不更新了，也就没什么人用了。因此我感觉学习一个框架，还是有必要了解它的`编程思想`。

先简单介绍下目前咱们已知的`编程思想`。

2.1 `面向过程`：处理事情以过程为核心，一步一步的实现。

2.2 `面向对象`：万物皆对象

2.3 `链式编程思想`：是将多个操作（多行代码）通过点号(.)链接在一起成为一句代码,使代码可读性好。a(1).b(2).c(3)

*	`链式编程特点`：方法的返回值是block,block必须有返回值（本身对象），block参数（需要操作的值）

*	`代表`：masonry框架。

*	`练习一`:模仿masonry，写一个加法计算器，练习链式编程思想。

2.4 `响应式编程思想`：不需要考虑调用顺序，只需要知道考虑结果，类似于蝴蝶效应，产生一个事件，会影响很多东西，这些事件像流一样的传播出去，然后影响结果，借用面向对象的一句话，万物皆是流。

*	`代表`：KVO运用。

*	`练习二`:KVO底层实现。


2.5 `函数式编程思想`：是把操作尽量写成一系列嵌套的函数或者方法调用。

*	`函数式编程特点`：每个方法必须有返回值（本身对象）,把函数或者Block当做参数,block参数（需要操作的值）block返回值（操作结果）

*	`代表`：ReactiveCocoa。

*	`练习三`:用函数式编程实现，写一个加法计算器,并且加法计算器自带判断是否等于某个值.

```

// 把每一个操作都写成一连串的函数或者方法，使代码高度聚合，便于管理.
// 每次都可以调用方法，因此肯定要每次都返回自己。

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 2  * 5 == 10
    Caculator *c = [[Caculator alloc] init];
    
    // 计算2  * 5 ，并且判断是否等于10
   BOOL isqule = [[[c caculator:^(int result) {
        result += 2;
        result *= 5;
        return result;
    }] equle:^BOOL(int result) {
        
        return result == 10;
        
    }] isEqule];
    
    NSLog(@"%d",isqule);
    
}

__________________________________________________
- (Caculator *)caculator:(int(^)(int result))caculatorOperation{
    
    _result += caculatorOperation(_result);
    
    return self;
}
- (Caculator *)equle:(BOOL (^)(int))operation
{
    _isEqule = operation(_result);
    
    return self;
}


```

#### 3.ReactiveCocoa编程思想
ReactiveCocoa结合了几种编程风格：

`函数式编程（Functional Programming）`

- 闭包&高阶函数
- 惰性计算
- 不改变状态
- 递归 

`响应式编程（Reactive Programming）`

所以，你可能听说过ReactiveCocoa被描述为函数响应式编程（FRP）框架。

以后使用RAC解决问题，就不需要考虑调用顺序，直接考虑结果，把每一次操作都写成一系列嵌套的方法中，使代码高聚合，方便管理。
