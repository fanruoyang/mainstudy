####  1 修改APP的 状态栏颜色
- 1 单个控制器修改

```
- (UIStatusBarStyle)preferredStatusBarStyle
{
    return UIStatusBarStyleLightContent;
}
```
####  2 毛玻璃效果

- 1 美工
- 2 第三方框架
- 3 CoreImage：图片的高斯模糊
- 4 UIToolBar