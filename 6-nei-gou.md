###### http://www.cocoachina.com/ios/20171207/21466.html?utm_source=tuicool&utm_medium=referral
#### 1 理解
- 1 内购的产品内容，详细信息需要从苹果获取
- 2 我们用来展示的内容，最好还是跟服务器获取，然后拿到对应的ID,在去请求所有的信息
- 3 购买成功后，需要进行验证
   - 一般是让服务端认证，安全效率
   - 一个是移动端自己进行验证,下面是代码
   - 认证的时候要注意测试环境，沙盒测试，和正式购买的地址参数不一样
- 4 购买流程
  - 用户先拿到购买产品的单子，
  - 拿着单子去苹果那里交钱，交完钱让苹果在单子上盖个章
  - 拿着盖了章的单子传给自己的服务器来验证是否真的支付成功
  - 根据服务器返回的信息做具体的处理   
   
```
#pragma mark 客户端验证购买凭据
- (void)verifyTransactionResult
{
    // 验证凭据，获取到苹果返回的交易凭据
    // appStoreReceiptURL iOS7.0增加的，购买交易完成后，会将凭据存放在该地址
    NSURL *receiptURL = [[NSBundle mainBundle] appStoreReceiptURL];
    // 从沙盒中获取到购买凭据
    NSData *receipt = [NSData dataWithContentsOfURL:receiptURL];
    // 传输的是BASE64编码的字符串
    /**
     BASE64 常用的编码方案，通常用于数据传输，以及加密算法的基础算法，传输过程中能够保证数据传输的稳定性
     BASE64是可以编码和解码的
     */
    NSDictionary *requestContents = @{
                                      @"receipt-data": [receipt base64EncodedStringWithOptions:0]
                                      };
    NSError *error;
    // 转换为 JSON 格式
    NSData *requestData = [NSJSONSerialization dataWithJSONObject:requestContents
                                                          options:0
                                                            error:&error];
    // 不存在
    if (!requestData) { /* ... Handle error ... */ }

    // 发送网络POST请求，对购买凭据进行验证
    NSString *verifyUrlString;
#if (defined(APPSTORE_ASK_TO_BUY_IN_SANDBOX) && defined(DEBUG))
    verifyUrlString = @"https://sandbox.itunes.apple.com/verifyReceipt";
#else
    verifyUrlString = @"https://buy.itunes.apple.com/verifyReceipt";
#endif
    // 国内访问苹果服务器比较慢，timeoutInterval 需要长一点
    NSMutableURLRequest *storeRequest = [NSMutableURLRequest requestWithURL:[[NSURL alloc] initWithString:verifyUrlString] cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];

    [storeRequest setHTTPMethod:@"POST"];
    [storeRequest setHTTPBody:requestData];

    // 在后台对列中提交验证请求，并获得官方的验证JSON结果
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];


    [NSURLConnection sendAsynchronousRequest:storeRequest queue:queue
                           completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {
                               if (connectionError) {
                                   NSLog(@"链接失败");
                               } else {
                                   NSError *error;
                                   NSDictionary *jsonResponse = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
                                   if (!jsonResponse) {
                                       NSLog(@"验证失败");
                                   }

                                   // 比对 jsonResponse 中以下信息基本上可以保证数据安全
                                   /*
                                    bundle_id
                                    application_version
                                    product_id
                                    transaction_id
                                    */

                                   NSLog(@"验证成功");
                               }
                           }];

}

```
#### 2 代码

- 