
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
//返回pickerView有多少列
- (NSInteger)numberOfComponentsInPickerView:(UIPickerView *)pickerView
{
    return 1;
}
//返回多少行
- (NSInteger)pickerView:(UIPickerView *)pickerView numberOfRowsInComponent:(NSInteger)component{
     return 1;
}
//返回对应的自定义筛选 view 或者对应的 Nsstring
- (UIView *)pickerView:(UIPickerView *)pickerView viewForRow:(NSInteger)row forComponent:(NSInteger)component reusingView:(UIView *)view
{
    XMGFlagView *flagView = [[NSBundle mainBundle] loadNibNamed:@"XMGFlagView" owner:nil options:nil][0];
    
    // 取出对应的模型
    XMGFlag *flag = self.flags[row];
    flagView.flag = flag;
    
    return flagView;
}
/**
 *  返回第component第row行的文字 ****同上
 *
 */
- (NSString *)pickerView:(UIPickerView *)pickerView titleForRow:(NSInteger)row forComponent:(NSInteger)component
{
    NSString *food = self.foods[component][row];
    
    return food;
}

/**
 *  选中第component列第row行的时候调用
 *
 */
- (void)pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component
{
    switch (component) {
        case 0:
            _fruitLabel.text = self.foods[component][row];
            break;
        case 1:
            _mainLabel.text = self.foods[component][row];
            break;
        case 2:
            _drinkLabel.text = self.foods[component][row];
            break;
    }
    
}
//返回的row 高度
- (CGFloat)pickerView:(UIPickerView *)pickerView rowHeightForComponent:(NSInteger)component
{
    return 90;
}
```
#### BUG
- 在进行联动的时候会出现键值角标问题报错。需要每次进行刷新

```
- (void)pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component
{
    
    if (component == 0) { // 选中省会，刷新城市
        
        // 记录省会角标
        _selectProvinceIndex = [pickerView selectedRowInComponent:0];
        
        // 刷新城市列表
        [pickerView reloadComponent:1];
        
        // 默认选中第一个城市
        [pickerView selectRow:0 inComponent:1 animated:NO];
        
    }
        // 获取选中的省会
        HMProvince *province = self.provinces[_selectProvinceIndex];

        // 获取选中的城市名称
        NSInteger cIndex = [pickerView selectedRowInComponent:1];
        NSString *cityName = province.cities[cIndex];
        _cityField.text = [NSString stringWithFormat:@"%@  %@",province.name,cityName];
    
}
```

#### 时间选择器
- 随便点击，点击出一般的选择控制器

```
/*
 UIDatePickerModeTime,           12小时制 小时 分
 UIDatePickerModeDate,           年 月 日
 UIDatePickerModeDateAndTime,    月 日 小时 分
 UIDatePickerModeCountDownTimer, 24小时制 小时 分
 };
 */

- (void)setUpBirthdayKeyboard
{
    UIDatePicker *birthdayKeyboard = [[UIDatePicker alloc] init];
    // 设置地区
    birthdayKeyboard.locale = [NSLocale localeWithLocaleIdentifier:@"zh"];
    birthdayKeyboard.datePickerMode = UIDatePickerModeDate;
    [birthdayKeyboard addTarget:self action:@selector(dateChange:) forControlEvents:UIControlEventValueChanged];
    _birthdayField.inputView = birthdayKeyboard;
}

- (void)dateChange:(UIDatePicker *)datePicker
{
    // 创建日期格式字符串
    NSDateFormatter *fmt = [[NSDateFormatter alloc] init];
    fmt.dateFormat = @"yyyy-MM-dd";
    NSString *timeStr = [fmt stringFromDate:datePicker.date];
    _birthdayField.text = timeStr;
}

```
- 系统默认的时间选择器

```
-(void)riqiTap
{ 
 UIDatePicker *datePicker = [[UIDatePicker alloc] init]; datePicker.datePickerMode = UIDatePickerModeDateAndTime
;
   
 UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"\n\n\n\n\n\n\n\n\n\n\n\n" message:nil preferredStyle:UIAlertControllerStyleActionSheet];
   [alert.view addSubview:datePicker];
    UIAlertAction *ok = [UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:^(UIAlertAction *action) {
        NSDateFormatter* dateFormat = [[NSDateFormatter alloc] init];
        //实例化一个NSDateFormatter对象
        [dateFormat setDateFormat:@"yyyy-MM-dd-HH-mm"];//设定时间格式
     //   [dateFormat1 setDateFormat:@"HH:mm"];//设定时间格式
        NSString *dateString = [dateFormat stringFromDate:datePicker.date];
        //求出当天的时间字符串   
 _textlabel3.text= dateString;
        _textlabel3.textColor = [UIColor blackColor];
        NSLog(@"%@",dateString);
    }];
    UIAlertAction *cancel = [UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleDefault handler:^(UIAlertAction *action) {
       }];
   [alert addAction:ok];
   [alert addAction:cancel];
    [self presentViewController:alert animated:YES completion
:^{ }];
}
```