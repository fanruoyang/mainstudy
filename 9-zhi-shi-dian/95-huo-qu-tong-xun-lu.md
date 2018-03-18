#### 1 AddressBookUI 自带UI 框架
- 提供了联系人列表界面、联系人详情界面、添加联系人界面等
- 一般用于选择联系人
- 桥接的内存泄漏，需要手动释放内存

```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    // 1.创建选择联系人的界面
    ABPeoplePickerNavigationController *ppnc = [[ABPeoplePickerNavigationController alloc] init];
    
    // 2.设置代理
    ppnc.peoplePickerDelegate = self;
    
    // 3.弹出选择联系人界面
    [self presentViewController:ppnc animated:YES completion:nil];
}

#pragma mark - 实现ABPeoplePickerNavigationController的代理方法
// 选中某一个联系人的时候,会执行该代理方法
// 如果实现了该方法,那么就不会进入联系人的详细界面
- (void)peoplePickerNavigationController:(ABPeoplePickerNavigationController *)peoplePicker didSelectPerson:(ABRecordRef)person
{
    /*
     __bridge NSString * : 将CoreFoundation框架的对象所有权交给Foundation框架来使用,但是Foundation框架中的对象并不能管理该对象内存
     __bridge_transfer NSString * : 将CoreFoundation框架的对象所有权交给Foundation来管理,如果Foundation中对象销毁,那么我们之前的对象(CoreFoundation)会一起销毁
     */
    // 1.获取选中联系人的姓名(姓lastname和名firstname)
    CFStringRef firstname = ABRecordCopyValue(person, kABPersonFirstNameProperty);
    CFStringRef lastname = ABRecordCopyValue(person, kABPersonLastNameProperty);
    NSString *firstName = (__bridge_transfer NSString *)(firstname);
    NSString *lastName = (__bridge_transfer NSString *)(lastname);
    NSLog(@"%@ %@", firstName, lastName);
    
    // 2.获取联系人的电话号码
    ABMultiValueRef phones = ABRecordCopyValue(person, kABPersonPhoneProperty);
    CFIndex count = ABMultiValueGetCount(phones);
    for (CFIndex i = 0; i < count; i++) {
        NSString *phoneLabel = (__bridge_transfer NSString *)ABMultiValueCopyLabelAtIndex(phones, i);
        NSString *phoneValue = (__bridge_transfer NSString *)ABMultiValueCopyValueAtIndex(phones, i);
        NSLog(@"label : %@ value : %@", phoneLabel, phoneValue);
    }
    
    // 3.释放不再使用的对象
    CFRelease(phones);
}

// 选择某一个联系人的某一个属性时,会执行该方法
// property选中的属性
// identifier : 每一个属性都由一个对应标示
// 如果实现了该方法,那么选中一个联系人的属性时,就会推出控制器.不会进入下一个页面
- (void)peoplePickerNavigationController:(ABPeoplePickerNavigationController *)peoplePicker didSelectPerson:(ABRecordRef)person property:(ABPropertyID)property identifier:(ABMultiValueIdentifier)identifier
{
    NSLog(@"选择了某一个联系人的某一个属性");
}

// 点击了取消按钮会执行该方法
- (void)peoplePickerNavigationControllerDidCancel:(ABPeoplePickerNavigationController *)peoplePicker
{
    
}

```
#### 2 AddressBook.framework  不带UI框架
- 纯C语言的API，仅仅是获得联系人数据
- 没有提供UI界面展示，需要自己搭建联系人展示界面
- 里面的数据类型大部分基于Core Foundation框架，使用起来极其蛋疼


 