#### 1 tableView 的一些小知识点
      1.如何让tableview 不滑动 
      self.UITableView.scrollEnabled = NO

      2.cell取消点击效果
      cell.selectionStyle = UITableViewCellSelectionStyleNone; 
      
      3.TableView的顶部有一部分空白区域，不是Cell  contenctInest
      self.automaticallyAdjustsScrollViewInsets = NO;
      
      关于tableview contenctInest 的设置，如果当前控制器有俩个，只对一个tabelView设置了，
      需要我们自己对另外的tableView设置向下64pt
      self.tabelView.contenctInest=UIEdgeInsetsMake(64,0,0,0);
##### scorllView 同样有这个方法-->导航栏存在苹果默认给第一个添加64 没有导航栏是添加 20

      4.关于选中cell 中的image的图片，系统有方法,选中cell的时候是默认显示高亮状态的图片和文字
        需要给image设置图片样式---> 普通和高亮，还有label
        cell.imageView.image=[];
        cell.imageView.highlightedImage=[];
        cell.textLabel.highlightedTextColor=[];
        
      5.默认选中第几行，并且存在点击效果。一般来说默认选中第一行
      //UITableViewScrollPositionTop 自由滚动到顶部
      [self.table_view selectRowAtIndexPath:[NSIndexPath indexPathForItem:0 inSection:0] animated:YES scrollPosition:UITableViewScrollPositionTop];
      [self tableView:self.table_view didSelectRowAtIndexPath:[NSIndexPath indexPathForItem:0 inSection:0]];//实现点击第一行所调用的方法  
      
      6.对应刷新
      //一个section刷新    
      NSIndexSet *indexSet=[[NSIndexSet alloc]initWithIndex:2];    
      [tableview reloadSections:indexSet withRowAnimation:UITableViewRowAnimationAutomatic];    
      //一个cell刷新    
     NSIndexPath *indexPath=[NSIndexPath indexPathForRow:3 inSection:0];    
     [tableView reloadRowsAtIndexPaths:[NSArray arrayWithObjects:indexPath,nil]   withRowAnimation:UITableViewRowAnimationNone];  
     
    