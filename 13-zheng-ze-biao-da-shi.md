##### 介绍
- 在编写处理字符串的程序时，经常会查找符合某些复杂规则的字符串需要，正则表达式就是用于描叙这些规则的的工具，换句话说，正则表达式就是记录文本规则的代码
- 在很多文本的编辑器里，可以使用正则表达式进行检索，Xcode同样支持正则表达式
- 几乎所有的程序编程都支持正则表达式

##### 练习

- 1 匹配abc
- 2 包含一个a-z,后面必须是0-9->[a-z][0-9]或者 [a-z]\d
[a-z]:指的是a到z任意一个
[a-z]\d：== [0-9]
- 3 必须第一个是字母，第二个是数字 --> ^[a-z][0-9]$
^表示首字母必须是A--z
{2，10}:2表示存在几位数,加 逗号表示或者的意思。数字到2到10个数字都可以
[a-z]$ :必须以a-z 的一个字母结尾
- 4 必须第一个是字母，字母后面跟上 4 - 9 个数字
- 5 不能是数字0-9
^[^0-9]:不能是0到9的数字
- 6 qq匹配：^[1-9]\d{4,11}$
- 都是数字
- 5 - 12位
- 并且第一位不能是0
- 7 手机号码匹配 ^1[3578]\d{9}$

```objc
/*
 
 @"@.*?:" : 第一个是@ . 表示后面是任意符号 * 表示 0个或者多个 ?遇到冒号就截取--不写就截取一个 :表示遇到冒号截取
 @"\\[.*？\\]" \\转义   匹配表情 [偷笑]
 */

extension RegistViewController{
    func setregex() {
        
        let string = "ffffaaffabcfgggabcdd"
   //问题1
        //1 创建正则表达式规则
        let pattern = "abc"
        //2 创建正则表达对象(try try? try!)
        let regex = try? NSRegularExpression(pattern :pattern)
        guard (regex != nil) else {
            
            return
        }
        //3 匹配字符串内容--返回的数组
        let results = regex?.matches(in: string, options: [], range: NSRange(location: 0, length: string.count))
        //4 遍历数组，获取结果
        for result in results! {
            FYLog(message: result.range)
        }
        
        
   //问题2
    }
    
}

```