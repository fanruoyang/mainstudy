#### 0 记得授权
#### 1 AddressBookUI 自带UI 框架 （IOS 9 弃用）
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
#### 2 AddressBook.framework  不带UI框架 （IOS 9 弃用）

- 纯C语言的API，仅仅是获得联系人数据
- 没有提供UI界面展示，需要自己搭建联系人展示界面
- 里面的数据类型大部分基于Core Foundation框架，使用起来极其蛋疼

```
    // 1.获取授权的状态
    ABAuthorizationStatus status = ABAddressBookGetAuthorizationStatus();
    
    // 2.判断授权状态,如果是未决定状态,才需要请求
    if (status == kABAuthorizationStatusNotDetermined) {
        // 2.1.创建通信录对象
        ABAddressBookRef addressBook = ABAddressBookCreateWithOptions(NULL, NULL);
        
        // 2.2.请求授权
        ABAddressBookRequestAccessWithCompletion(addressBook, ^(bool granted, CFErrorRef error) {
            if (granted) {
                NSLog(@"授权成功");
            } else {
                NSLog(@"授权失败");
            }
        });
    }
    

```
- 使用方法

```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    // 1.获取授权的状态
    ABAuthorizationStatus status = ABAddressBookGetAuthorizationStatus();
    
    // 2.如果用户已经授权
    if (status != kABAuthorizationStatusAuthorized) return;
    
    // 3.创建通信录对象
    ABAddressBookRef addressBook = ABAddressBookCreateWithOptions(NULL, NULL);
    
    // 4.从通信录对象中,将所有的联系人拷贝出来
    CFArrayRef peopleArray = ABAddressBookCopyArrayOfAllPeople(addressBook);
    
    // 5.遍历所有的联系人(每一个联系人都是一条记录)
    CFIndex peopleCount = CFArrayGetCount(peopleArray);
    for (CFIndex i = 0; i < peopleCount; i++) {
        
        // 6.获取到联系人
        ABRecordRef person = CFArrayGetValueAtIndex(peopleArray, i);
        
        // 7.获取姓名
        NSString *lastname = (__bridge_transfer NSString *)ABRecordCopyValue(person, kABPersonLastNameProperty);
        NSString *firstName = (__bridge_transfer NSString *)ABRecordCopyValue(person, kABPersonFirstNameProperty);
        NSLog(@"%@ %@", lastname, firstName);
    }
    
    // 8.释放不再使用的对象
    CFRelease(peopleArray);
    CFRelease(addressBook);
}


```

#### 3 Contacts 框架 IOS 9以后
- 当使用这个框架的时候, 系统会自动发送授权申请, 不需要手动授权
- 有UI

```
    CNContactPickerViewController * contactPickerVc = [CNContactPickerViewController new];

    contactPickerVc.delegate = self;

    [self presentViewController:contactPickerVc animated:YES completion:nil];
#pragma mark - 选中一个联系人
- (void)contactPicker:(CNContactPickerViewController *)picker didSelectContact:(CNContact *)contact{

    NSLog(@"contact:%@",contact);
    //phoneNumbers 包含手机号和家庭电话等
    for (CNLabeledValue * labeledValue in contact.phoneNumbers) {

        CNPhoneNumber * phoneNumber = labeledValue.value;

        NSLog(@"phoneNum:%@", phoneNumber.stringValue);

    }
}

#pragma mark - 选中一个联系人属性
- (void)contactPicker:(CNContactPickerViewController *)picker didSelectContactProperty:(CNContactProperty *)contactProperty{

    NSLog(@"contactProperty:%@",contactProperty);
}

#pragma mark - 选中一个联系人的多个属性
- (void)contactPicker:(CNContactPickerViewController *)picker didSelectContactProperties:(NSArray<CNContactProperty*> *)contactProperties{

    NSLog(@"contactPropertiescontactProperties:%@",contactProperties);
}

#pragma mark - 选中多个联系人
- (void)contactPicker:(CNContactPickerViewController *)picker didSelectContacts:(NSArray<CNContact*> *)contacts{

    NSLog(@"contactscontacts:%@",contacts);
}

```

- 没UI

```
    if (event.type == 0) {
        //判断授权状态
        if ([CNContactStore authorizationStatusForEntityType:CNEntityTypeContacts] == CNAuthorizationStatusNotDetermined) {

            CNContactStore *store = [[CNContactStore alloc] init];
            [store requestAccessForEntityType:CNEntityTypeContacts completionHandler:^(BOOL granted, NSError * _Nullable error) {
                if (granted) {
                    NSLog(@"授权成功");
                    // 2. 获取联系人仓库
                    CNContactStore * store = [[CNContactStore alloc] init];

                    // 3. 创建联系人信息的请求对象
                    NSArray * keys = @[CNContactGivenNameKey, CNContactFamilyNameKey, CNContactPhoneNumbersKey];

                    // 4. 根据请求Key, 创建请求对象
                    CNContactFetchRequest * request = [[CNContactFetchRequest alloc] initWithKeysToFetch:keys];

                    // 5. 发送请求
                    [store enumerateContactsWithFetchRequest:request error:nil usingBlock:^(CNContact * _Nonnull contact, BOOL * _Nonnull stop) {

                        // 6.1 获取姓名
                        NSString * givenName = contact.givenName;
                        NSString * familyName = contact.familyName;
                        NSLog(@"%@--%@", givenName, familyName);

                        // 6.2 获取电话
                        NSArray * phoneArray = contact.phoneNumbers;
                        for (CNLabeledValue * labelValue in phoneArray) {

                            CNPhoneNumber * number = labelValue.value;
                            NSLog(@"%@--%@", number.stringValue, labelValue.label);
                        }
                    }];
                } else {
                    NSLog(@"授权失败");
                }
            }];
        }

```

#### 4 第三方框架 


 