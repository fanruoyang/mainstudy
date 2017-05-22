# Autolayout


#### 简单属性
-  xib中的设置
-  blueView.autoreszizingMask=UIViewAuore...
  //自动伸缩几个枚举，先用frame建立x,y ，然后建立对应的控件适应

####注意 自动适应和frame 不能同时使用，会产生冲突
## 使用代码实现Autolayout的方法1
- 创建约束

```objc
+(id)constraintWithItem:(id)view1
attribute:(NSLayoutAttribute)attr1
relatedBy:(NSLayoutRelation)relation
toItem:(id)view2
attribute:(NSLayoutAttribute)attr2
multiplier:(CGFloat)multiplier
constant:(CGFloat)c;
* view1 ：要约束的控件
* attr1 ：约束的类型（做怎样的约束）
* relation ：与参照控件之间的关系
* view2 ：参照的控件
* attr2 ：约束的类型（做怎样的约束）
* multiplier ：乘数
* c ：常量
```

- 添加约束

```objc
- (void)addConstraint:(NSLayoutConstraint *)constraint;
- (void)addConstraints:(NSArray *)constraints;
```

- 注意
    - 一定要在拥有父控件之后再添加约束
    - 关闭Autoresizing功能
    ```objc
    view.translatesAutoresizingMaskIntoConstraints = NO;
    ```

## 使用代码实现Autolayout的方法2 - VFL
- 使用VFL创建约束数组

```objc
+ (NSArray *)constraintsWithVisualFormat:(NSString *)format
options:(NSLayoutFormatOptions)opts
metrics:(NSDictionary *)metrics
views:(NSDictionary *)views;
* format ：VFL语句
* opts ：约束类型
* metrics ：VFL语句中用到的具体数值
* views ：VFL语句中用到的控件
// 添加水平方向的约束
    NSString *vfl = @"H:|-margin-[blueView]-margin-[redView(==blueView)]-margin-|";
    NSDictionary *views = NSDictionaryOfVariableBindings(blueView, redView);
    NSDictionary *mertrics = NSDictionaryOfVariableBindings(margin);
    NSArray *constraints = [NSLayoutConstraint constraintsWithVisualFormat:vfl options:NSLayoutFormatAlignAllTop | NSLayoutFormatAlignAllBottom metrics:mertrics views:views];
    [self.view addConstraints:constraints];
 // 添加竖直方向的间距
    NSNumber *height = @40;
    NSString *vfl2 = @"V:[blueView(height)]-margin-|";
    NSDictionary *mertrics2 = NSDictionaryOfVariableBindings(margin, height);
    NSArray *constraints2 = [NSLayoutConstraint constraintsWithVisualFormat:vfl2 options:kNilOptions metrics:mertrics2 views:views];
    [self.view addConstraints:constraints2];

```

- 使用下面的宏来自动生成views和metrics参数

```objc
NSDictionaryOfVariableBindings(...)
```

## 使用代码实现Autolayout的方法3 - Masonry
- 使用步骤
    - 添加Masonry文件夹的所有源代码到项目中
    - 添加2个宏、导入主头文件
    ```objc
    // 只要添加了这个宏，就不用带mas_前缀
    #define MAS_SHORTHAND
// 只要添加了这个宏，equalTo就等价于mas_equalTo
#define MAS_SHORTHAND_GLOBALS
// 这个头文件一定要放在上面两个宏的后面
#import "Masonry.h"
    ```

- 添加约束的方法

```objc
// 这个方法只会添加新的约束
 [view makeConstraints:^(MASConstraintMaker *make) {

 }];

// 这个方法会将以前的所有约束删掉，添加新的约束
 [view remakeConstraints:^(MASConstraintMaker *make) {

 }];

 // 这个方法将会覆盖以前的某些特定的约束
 [view updateConstraints:^(MASConstraintMaker *make) {

 }];

```

- 约束的类型
```objc
1.尺寸：width\height\size
2.边界：left\leading\right\trailing\top\bottom
3.中心点：center\centerX\centerY
4.边界：edges
5.multipler属性表示约束值为约束对象的乘因数, dividedBy属性表示约束值为约束对象的除因数，可用于设置view的宽高比
6.priorityLow()设置约束优先级
7.如果想要约束变换之后实现动画效果，则需要执行如下操作
// 通知需要更新约束，但是不立即执行
//可以看官方文档
[self setNeedsUpdateConstraints];
// 立即更新约束，以执行动态变换
// update constraints now so we can animate the change
[self updateConstraintsIfNeeded];
// 执行动画效果, 设置动画时间
[UIView animateWithDuration:0.4 animations:^{
   [self layoutIfNeeded];
}];
```
