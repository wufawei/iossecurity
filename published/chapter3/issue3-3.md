####简介
SQLite，是一款轻型的数据库，是遵守ACID的关联式数据库管理系统，它的设计目标是嵌入式的，iOS 和 Android都支持。

如果有很多数据要存在本地，保存在SQLite数据库是一个很常见的做法，很多iOS应用都是这样做的。

在iOS逆向工程中，有时候需要把iOS设备中的SQLite数据库文件拷贝到Mac上，然后用工具打开，常用的有
SQLite Database Browser和SQLiteManager。


####下载SQLite Database Browser

SQLite Database Browser可以从这里[下载](http://sourceforge.net/projects/sqlitebrowser/)。

下载之后安装运行，如下图所示：

![](https://farm4.staticflickr.com/3670/14266454411_63b8d9f624_z.jpg)

####SQLite Database Browser用法

用前面介绍的工具iFunbox(如果你不知道这个工具，请阅读本系列前面的文章)从微信的目录下拷贝出一个SQLite数据库文件：

![](https://farm6.staticflickr.com/5520/14083122428_14c73ce88b_z.jpg)



用SQLite Database Browser打开之后，点击Table可以得到一个下拉列表，显示所有的Table，如下图所示：

![](https://farm4.staticflickr.com/3689/14083172820_85a9036fc2_z.jpg)


选择其中的Friend这个表，然后点击第一个tab**Database structure**可以查看表结构，第二个tab**browse data**可以查看保存的数据，如下图所示：

![](https://farm6.staticflickr.com/5516/14269350354_22b82a7a56_z.jpg)


其中的UsrName这一列就可以用来添加好友了，比如在微信中添加好友的输入框中输入**gh_62efaa4930af**就可以添加李开复。


点击第三个Tab，可以输入需要执行SQL语句。

![](https://farm6.staticflickr.com/5497/14083226010_3fee9caeac_z.jpg)


####小结

本文简单介绍了SQLite工具SQLite Database Browser的用法。SQLiteManager也可以完成类似的功能，读者可以下载体验下这个工具。

可以看出，应用保存在iOS设备上的SQLite数据，是很容易被他人获取的，设备无需越狱。

***
[#3 Mac上需要安装的工具下的更多文章](http://security.ios-wiki.com/issue-3/)