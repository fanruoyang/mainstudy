##### 1  拿到ipa 包    
      1 iTunes 12.6.3  https://support.apple.com/en-us/HT208079
      不兼容macOS Mojave
      2 PP助手
##### 2 签名工具  
       1 codesing  命令  codesign -d -vv WeChat.app
       2 查看本机电脑证书 security find-identity -v -p codesigning 
                      otool - l WeChap.app  获取信息，但是我获取不到
       3 获取加密标识 grep cryptid 0 没有加密，1 加密方案  
##### 3 操作
     1 打开包内容中 删除插件，不需要重签名的    PlugIns Watch  
     2 签名 Frameworks  
     codesign -fs"iPhone Developer: 990219310@qq.com (8Q3TP2Z4G5)" QBar.framework 
     3 注意搜索一个项目名称，需要变黑 变成可执行文件
     4 新建添加描述文件  真机运行 
     5 将真机的描述文件放到越狱的文件夹里面，找到plist 文件修改bund id
     6 security cms -D -i embedded.mobileprovision 打开描述文件获取信息
     7 get-task-allow 为true 的时候可以调试
     8 Entitlements 的值需要拷贝出来新建plist 文件 签名要用
     9 建立文件夹放俩个文件 一个plis 和 app
     10 签名 codesign -fs"iPhone Developer: 990219310@qq.com (8Q3TP2Z4G5)" --no-strict --entitlements=Entitlements.plist WeChat.app 不要强制严格审查/注意大写字母plist文件的
     11 查看签名是否成功  codesign -d -vv WeChat.app
     12 安装app 建立Payload 文件放入app
     13 打包压缩 zip -ry WeChat.ipa Payload
     14 打开xcode command+shift+2 打开连接设备管理
                         
##### 4 一直报错，描述文件不准确