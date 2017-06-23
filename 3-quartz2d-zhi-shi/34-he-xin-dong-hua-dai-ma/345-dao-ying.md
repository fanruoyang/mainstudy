#05-倒影

###实现思路：
1.用复制图层实现，搞个UIImageView展示图片，然后复制UIImageView.

2.注意：`复制图层只能复制子层，但是UIImageView只有一个主层，并没有子层，因此不能直接复制UIImageView`.

3.正确做法:应该把UIImageView添加到一个UIView上，然后复制UIView的层，就能复制UIImageView.

注意：`默认A控件是B控件的子控件，那么A控件的层就是B控件的层的子层。`

4.但是有问题，默认UIView的层不是复制层，我们想把UIView的层变成复制层，重写+layerClass方法。

```
+ (Class)layerClass
{
    return [CAReplicatorLayer class];
}
```


5.倒影效果：就是就是把复制图片旋转180度，然后往下平移，最好先偏移在，在旋转。

```
	 CAReplicatorLayer *layer = (CAReplicatorLayer *)self.v.layer;

    layer.instanceCount = 2;


    // 先Y轴偏移
    CATransform3D transform =  CATransform3DMakeTranslation(0, self.v.bounds.size.height, 0);

    // 在旋转
    transform = CATransform3DRotate(transform, M_PI, 1, 0, 0);

    // 设置复制层的形变
    layer.instanceTransform = transform;

    // 设置颜色通道偏移量，相等上一个一点偏移量，就是阴影效果
    layer.instanceRedOffset = -0.1;
    layer.instanceGreenOffset = -0.1;
    layer.instanceBlueOffset = -0.1;
    layer.instanceAlphaOffset = -0.1;
```
