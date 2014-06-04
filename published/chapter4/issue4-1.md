
### 引言

本节将简要介绍iOS设备越狱的步骤。

### 越狱你的设备
如果你真的对iOS安全很感兴趣，有一个越狱设备是非常有必要的。在本节，我们将介绍如何越狱iOS设备。越狱之后有很多好处，你可以安装很多工具，例如nmap, metasploit，甚至在设备上运行自己写的python代码。

越狱非常简单，下载一个越狱软件，然后点击越狱就可以了。如果你的设备运行的是iOS 6.x到iOS 7.0.6的系统，推荐你使用[evasi0n][1]，如果你的设备运行的是iOS 5.x，那推荐用[redsn0w][2]。

目前iOS 7.1到iOS 7.1.1已经能越狱，但是越狱方法还没有公布出来，应该要iOS 8发布之后，才会公布。


请注意，在越狱之前，一定要先用iTunes或者iCloud备份一下，这样即使出错，你也能够恢复你的数据。


请注意：以下越狱部分的介绍来自[这里](http://wufawei.com/2013/11/ios-application-security-1/)

本文将对我的new iPad （3代，运行 iOS 6.0.1）越狱。一旦你下载evasi0n并运行，它就会自动检测设备并且告诉你这个设备是否可以越狱。 如图：

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat1.png)

你需要做的仅仅是点击jailbeak(越狱)，然后让evasi0n做剩下的事情。

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat2.png)


你可以看到，越狱已经开始了。过一段时间之后，evasi0n将会重启设备，然后运行exploit（利用程序）。
![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat3.png)


一旦利用程序（exploit）运行完毕，它就会安装Cydia和Cydia的包列表到设备上。Cydia是一个图形界面，使得你能在越狱设备上下载和安装软件包和应用，这些应用通常你在App store是找不到的。基本上所有的越狱程序都会默认安装Cydia。你可以认为Cydia就是越狱设备上的App Store。

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat4.png)


等待一段时间，直到你得到下面的提示。 

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat5.png)


解锁你的设备，你可以看到桌面上有个新的app图标，叫做Jailbreak。点击它以完成越狱过程。

![](http://resources.infosecinstitute.com/wp-content/uploads/042413_1342_IOSApplicat6.png)


你可以看到你的设备将会重启。请耐心等待这个过程的完成。一旦设备完成重启，你可以看到一个叫做Cydia的新app在桌面上。这个就表明你已经成功越狱。

祝贺你，你已经在iOS hacking领域跨出了第一步。


###小结
 
本文简要介绍了如何对iOS设备越狱，并且以iOS 6为例，我手头没有可以越狱的iOS 7设备，因此，这里没有介绍，步骤其实也类似。后续iOS7.1.1可以越狱了，这里再更新下教程。

越狱需谨慎，请一定备份数据。

对于普通用户，从安全的角度考虑，**建议不要越狱**。

***
[#4 iOS设备上的工具下的更多文章](http://security.ios-wiki.com/issue-4/)


[1]: http://evasi0n.com/
[2]: http://www.redsn0w.us/
