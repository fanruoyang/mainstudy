#### Socket

#####1  TCP&UDP
- TCP（传输控制协议）
建立连接，形成传输数据的通道
在连接中进行大数据传输（数据不受限制）
通过三次握手完成连接，是可靠协议，安全送达
必须建立连接，效率会稍低

- UDP（用户数据报协议）
将数据及源和目的封装成数据包中，不需要建立连接
每个数据报的大小限制在64K之内
因为无需连接，因此是不可靠协议
不需要建立连接，速度快

- 1.HTTP 底层就是通过socket建立连接通信管道，实现数据传输
- 2.HTTP是一个TCP的传输协议(方式)，它是一个可靠，安全的协议


##### 2 实现Socket服务端监听

- 使用C语言实现，
- 使用CocoaAsyncSocket第三方框，内部是对C的封装
- Telnet命令 telnet host port/telnet 192.168.10.10 5288
- telnet命令是连接服务器上的某个端口对应的服务

##### 3 CocoaAsyncSocket的使用
 - 服务端创建---一般后台做，
 
```

       //1.创建一个服务监听对象
        FYSevericeListener *listener=[[FYSevericeListener alloc]init];
        //2。开启监听
        [listener start];
        //3. 开启主运行循环，让服务不能停
        [[NSRunLoop mainRunLoop]  run];
        
        
        
  -(void)start{
    
    // 开启10086服务
    //1. 创建一个scocket对象
    //serverSocket 服务端的socket只监听，有没有客户端请求链接
    GCDAsyncSocket *serverSocket=[[GCDAsyncSocket alloc]initWithDelegate:self delegateQueue:dispatch_get_global_queue(0, 0)];
    
    //2. 绑定端口-并且监听
    
    NSError *error = nil;
    
    [serverSocket acceptOnPort:5288 error:&error];
    
    if (!error) {
        NSLog(@"10086服务开启成功");
    }else{
        //原因一 端口被其他占用了
        NSLog(@"10086服务绑定失败%@",error);
    }
    
    self.serverSocket=serverSocket;
    //3. 开启监听
    
}

 #pragma  mark ----------------   有客户端socket连接到服务器  ----------------

-(void)socket:(GCDAsyncSocket *)sock didAcceptNewSocket:(GCDAsyncSocket *)newSocket{
    
    NSLog(@"serverSocket________%@",sock);
    NSLog(@"serverSocket________%@",newSocket);
    //1 .监听客户端的scoket
    [self.clientSockets addObject:newSocket];
    
#warning 客户端一连接，就给客户端提供服务，发消息
    NSMutableString *serviceString=[NSMutableString string];
    [serviceString appendString:@"欢迎来到我的地盘"];
    [serviceString appendString:@"挨打请输入1"];
    [serviceString appendString:@"吃饭请输入2"];
    [serviceString appendString:@"逛街请输入3"];
    [serviceString appendString:@"看电影请输入4"];
    
    [newSocket writeData:[serviceString dataUsingEncoding:NSUTF8StringEncoding] withTimeout:-1 tag:0];
    //2 .监听客户端有没有数据上传
    //timeout -1 代表不超时
    //tag 标识作用，现在不用;默认0
    [newSocket readDataWithTimeout:-1 tag:0];
}
//读取客户端请求的数据
//data 为传送过来的数据
-(void)socket:(GCDAsyncSocket *)sock didReadData:(NSData *)data withTag:(long)tag{
    
    //收到的信息转成字符串
    NSString *clientStr=[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
    
    NSString *log=[NSString stringWithFormat:@"IP_____%@ data:_______%hu",sock.connectedHost,sock.connectedPort];
    
    
    //将收到的信息返回给客户端
    [sock writeData:data withTimeout:-1 tag:0];
#warning 每次读完数据都需要在进行监听
    [sock readDataWithTimeout:-1 tag:0];
}

-(void)socket:(GCDAsyncSocket *)sock didWriteDataWithTag:(long)tag{
    
    NSLog(@"数据发送成功");
}
       
        
```

- 移动端实现

```
    //连接到聊天服务器
    //1. 创建一个scocket对象
    //serverSocket 服务端的socket只监听，有没有客户端请求链接
    GCDAsyncSocket *serverSocket=[[GCDAsyncSocket alloc]initWithDelegate:self delegateQueue:dispatch_get_global_queue(0, 0)];
    
    //2. 绑定端口-并且监听
    
    NSError *error = nil;
    
    [serverSocket connectToHost:@"127.0.0.1" onPort:5288 error:&error];
    
    self.serverSocket=serverSocket;
    
  #pragma  mark ----------------   接受服务端的数据  ----------------
-(void)socket:(GCDAsyncSocket *)sock didReadData:(NSData *)data withTag:(long)tag{
    
    //1. 读取数据 进行处理
    
    //2 每次读取完数据，要再次监听
    
    [sock readDataWithTimeout:-1 tag:0];
}

#pragma  mark ----------------   连接主机成功  ----------------

-(void)socket:(GCDAsyncSocket *)sock didConnectToHost:(NSString *)host port:(uint16_t)port{
    
    // 监听数据
    
    [self.serverSocket readDataWithTimeout:-1 tag:0];
}

//发出数据
-(void)sendMessage{
    NSData *data;
    
    [self.serverSocket writeData:data withTimeout:-1 tag:0];
    
}
-(void)socketDidDisconnect:(GCDAsyncSocket *)sock withError:(NSError *)err{
    
    NSLog(@"连接主机失败");
}
  
    
```


