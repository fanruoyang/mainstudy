
# UIPickView
- 简单的执行选择，强制选中
```objc
// 让PickView选中某一行，不会主动调用代理方法，只有用户手动滚动pickView才会调用代理方法
[self.pickerView selectRow:row inComponent:i animated:YES];
// 主动调用
[self pickerView:nil didSelectRow:row inComponent:i];
```
- pickView的一些代理方法

```objc
//开始进入界面有个默认选中对应的数据 第一二三对应的0行
for (int i = 0; i < 3; i++) {
[self pickerView:nil didSelectRow:0 inComponent:i];
}
```