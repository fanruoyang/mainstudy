
####1 SVN 是集中式源代码管理工具

概念：
1> Repository   代码仓库，保存代码的仓库
2> Server       服务器，保存所有版本的代码仓库
3> Client       客户端，只保存当前用户的代码仓库
4> 用户名&密码   访问代码仓库需要使用自己的"用户名和密码"，从而可以区分出不同的人对代码做的修改

操作：
1> checkout     将服务器上最新的代码仓库下载到本地，"只需要做一次"
2> update       从服务器上将其他人所做的修改下载到本地，"每天上班必须要做的事情"
3> commit       将工作提交到服务器，"每天下班之前至少做一次"

#####SVN服务器安装 Visual SVN Server
--------------------------------------------------------------------------------

提示：一般程序员是没有机会安装SVN服务器的，以下内容仅供了解服务器上的安装及配置过程

* Visual SVN Server 是运行在windows操作系统上的，集成了Subversion和Apache
* 傻瓜式安装一步到位，并提供了图形化界面，安装简单配置方便
* 下载地址：http://www.visualsvn.com/server/download/

1>  安装
*   程序目录
*   代码仓库目录

2>  添加代码仓库"weibo"
*   暂时不勾选默认结构(trunk, branches, tags)

3>  添加用户
*   为了方便后续的演练，每个用户的口令不要设置成一样
-   用户名     口令
-   manager   manager
-   zhangsan  zhangsan
-   lisi      lisi

4>  添加群组，通过群组可以一次性设置多个用户对代码仓库的访问权限，便于管理

5>  设置权限演练
*   选中项目的代码库，点击右键，选择"Properties"

6>  协议
*   https   端口 443
*   http    端口 80

7>  协议的修改
*   选中"VisualSVN Server"，点击鼠标右键，选择"Properties"
*   选择"Network"，勾选"Use secure connection"会使用https协议访问服务器的代码仓库

补充：HTTPS = HTTP + SSL

--------------------------------------------------------------------------------
HTTPS(Hyper Text Transfer Protocol over Secure Socket Layer)是以安全为目标的HTTP通道，
简单讲是HTTP的安全版

HTTPS提供了身份验证与加密通讯方法，现在被广泛用于互联网上安全敏感的通讯，例如交易支付方面

HTTPS的证书需要申请认证，这一认证是付费的，认证网站 http://cn.globalsign.com/

8>  使用浏览器浏览代码仓库
如果使用了https协议，在不同的浏览器中访问，显示效果会不太一样

####	SVN演练准备
================================================================================

1>  显示隐藏文件夹
- 显示隐藏文件
$ defaults write com.apple.finder AppleShowAllFiles Yes && killall Finder
- 不显示隐藏文件
$ defaults write com.apple.finder AppleShowAllFiles No && killall Finder

2>  在桌面上新建一个文件夹 "svn演练"
依次建立以下三个目录
-   经理
-   张三
-   李四

####	命令行准备
================================================================================

熟悉命令行的目的：所有图形工具本质上都是对命令行的封装，理解命令行的操作，更有助于对图形工具的使用

1>  SVN基本命令
命令行格式：
svn <subcommand> [options] [args]
说明
svn 子命令 [选项] [参数]

提示：
-   [中括号]中包含的内容是可选的
-   (子命令缩写)

2>  查看帮助信息

$ svn help
查看svn所有命令的帮助
$ svn help 子命令

##### 撤销修改
$ svn revert Person.m

##### 恢复到之前的某个版本
 $ svn update -r 5
 $ svn up
##### 冲突解决
(p) postpone            对比
(mc) mine-conflict      使用我的
(tc) theirs-conflict    使用对方的
#### 2 一般使用SVN软件MAC Cornerstone 
#### 3 UNIX常用命令
1.  cd      改变工作目录
2.  pwd     输出当前工作目录的绝对路径
在UNIX中要执行什么命令，一定要知道自己当前所在的工作目录

3.  ls      查看文件
$ ls        显示文件
$ ls -a     显示所有文件
$ ls -l     列表显示文件
$ ls -la    列表显示所有文件

4.  touch   用于更改文件访问和修改时间的标准UNIX程序，也被用于创建新文件

   $ touch test.txt

注意：touch不修改test.txt内容，只更改它的访问、修改时间，如果test.txt不存在，它会被创建

5.  cat     连续查看文件内容

6.  more    分页查看文件内容

提示：
1>  命令和参数之间需要添加空格
2>  如果要使用当前目录中的文件名，输入到一半时，按TAB键能够补全

