#### 1 生命周期
#### 2 修改控制器大小 preferredContentSize
#### 3 UIModalPresentationPopover 带箭头

```
        // 2 设置大小
        popver.preferredContentSize = CGSize(width: 100, height: 200)
        //3 设置大小,这个我们也可以在控制器内部设置，让控制器自己决定
        popver.modalPresentationStyle = .popover
        //4 设置弹出的参照视图，rect参数是以view的左上角为坐标原点（0，0）
        popver.popoverPresentationController?.sourceView = sender as? UIView
        
        //5 弹出视图的尖参照视图底部的中间的位置,指定箭头所指区域的矩形框范围（位置和尺寸），以view的左上角为坐标原点
        popver.popoverPresentationController?.sourceRect = (sender as AnyObject).bounds;
        //6 这里就是去设置可以穿透的view，就是当控制器的view已经弹出来了，后面有蒙版了，我们点击后面的某个view是可以点击的，并且这个控制器的view不会消失
        //        popver.popoverPresentationController?.passthroughViews=[self.AlertButton];
        //设置弹出视图的箭头方向
        popver.popoverPresentationController?.permittedArrowDirections = .up
        
        //设置代理
        popver.popoverPresentationController?.delegate = self as? UIPopoverPresentationControllerDelegate
        
        self .present(popver, animated: true, completion: nil)
        
```