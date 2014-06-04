上一节我们介绍了如何对iOS设备越狱。
现在你已经完成了设备的越狱，那么下一步就是安装一些重要的命令行工具，例如 wget, ps, apt-get等用来审计iOS应用的工具。第一个要安装的就是OpenSSH。安装这个工具可以让你从mac登录进越狱设备。

**（请注意，以下文字来自我之前翻译的[文章](http://wufawei.com/2013/11/ios-application-security-1/)，原作者是：Prateek Gianchandani）**

进入Cydia，点击底部的搜索tab，然后搜索：OpenSSH.

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat7.png)


点击OpenSSH，然后点击安装，

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat8.png)


然后OpenSSH就会安装在你的设备上。
在我们用ssh登录进设备之前，我们 还需要安装其他一些命令行工具。几乎所有流行的黑客工具都可以在
BigBoss Recommendation tools这个包中找到。安装BigBoss Recommendation tools，在Cydia中搜索，然后点击安装。

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat9.png)

有些重要的命令行工具例如: APT 0.6 Transitional, Git, GNU Debugger, less, make, unzip, wget 和 SQLite 3.x会被安装好。


还有一个工具需要被安装: MobileTerminal，它能够让你在设备上直接运行命令行，而不是需要通过ssh登录设备运行命令。

在Cydia上搜索  MobileTerminal,然后安装。

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat10.png)



一旦安装好MobileTerminal,你就可以在桌面上看到一个新的app图标，名字叫做Terminal.

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat11.png)



点击它，你可以得到一个终端，试试运行几个Unix命令。在这里，我们简单的用ps列举下正在运行的进程。

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat12.png)


你可以看到，ps这个命令成功执行。


让我们看看我们是否能够通过ssh登录进这个越狱设备。确保你的电脑和越狱设备连接在同一个网络，找到越狱设备的IP地址。为了找到设备的IP地址，到设置（settings）- WiFi中选择设备现在连接的网络。

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat13.png)



可以看到，IP地址是192.168.2.3。让我们试试以root用户登录进去。在命令行中运行如下命令。root的默认密码是：alpine。推荐的做法是：一旦你安装好了OpenSSH, 马上改掉root的默认密码。这是因为有些恶意软件利用默认的用户名和密码登录进入设备[注1]。为了改密码，在ssh登录上之后，在命令行输入passwd这个命令，然后输入2次新密码。然后你的root秘密就已修改了。这一系列命令可以参见下图：

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat14.png)


备注：确保在运行时，Cydia在后台运行而不是在前台。这是因为Cydia以root运行，因此，如果Cydia在前台，而此时我们需要lock一个进程，而恰好此时该进程被Cydia lock，命令就不会运行成功。

运行一下 apt-get update, 获得最新的包列表

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/042413_1342_IOSApplicat15.png)


###小结

本文我们学习了如何越狱设备上搭建移动渗透测试平台，介绍了如何安装需要用到的工具。


[注1]，在iPhone上的第一个蠕虫，ikee利用了默认的密码，具体参见对ikee的详细分析报告：[An Analysis of the iKeeB (duh) iPhone botnet (Worm)](http://mtc.sri.com/iPhone/)


***
[#4 iOS设备上的工具下的更多文章](http://security.ios-wiki.com/issue-4/)


