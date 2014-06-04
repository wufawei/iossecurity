
要进行iOS逆向工程，建议掌握iOS应用的开发相关知识，相信看本文的读者应该都具备。
进行iOS逆向工程的一个关键就是工具的使用，工欲善其事，必先利其器。用好工具可以事半功倍。甚至可以做之前可能根本没想到能够做的事情。

这里介绍的工具可以分为如下几类：

* UI分析工具
* 文件系统查看工具
* 数据库查看工具
* 网络分析工具
* 逆向程序开发工具
* 反汇编工具
* 调试器




###UI分析工具
UI分析工具是对iOS应用的UI进行分析的工具，有[Reveal](http://revealapp.com/)和[PonyDebugger](https://github.com/square/PonyDebugger)等。


> Reveal能够在运行时调试和修改iOS应用程序。它能连接到应用程序，并允许开发者编辑各种用户界面参数，这反过来会立即反应在程序的UI上。就像用FireBug调试HTML页面一样，在不需要重写代码、重新构建和重新部署应用程序的情况下就能够调试和修改iOS用户界面。 --[InfoQ](http://www.infoq.com/cn/news/2013/07/debug_ios_apps_with_reveal)

使用Reveal的效果如图：

![](https://d262ilb51hltx0.cloudfront.net/max/1271/1*gh0UVZG3WXePXxHDDhyiww.png)

使用PonyDebugger的效果如图：

![](https://raw.githubusercontent.com/square/PonyDebugger/master/Documentation/Images/NetworkDebugging.png)

### 文件系统查看工具
在iOS设备上可以安装[iExplorer](http://www.macroplant.com/iexplorer/), [iFunbox](http://www.i-funbox.com/cn/ifunboxmac/), [iTool](http://www.itools.cn/itoolsmacjiantibanxiazai)等工具，可以查看iOS应用的文件系统结构。

使用iFunbox打开陌陌的文件目录，如下图所示：

![](https://farm4.staticflickr.com/3691/14074837820_98da877899_c.jpg)


###网络分析工具
使用Tcpdump, WireShark, Charles等工具可以对应用的网络数据进行分析。

使用Charles对网络数据包进行分析的示意图：

![](https://farm4.staticflickr.com/3665/14259408482_b10ba0245a_b.jpg)

###逆向程序开发工具

开发越狱程序和日常开发的iOS程序很相似，不过，越狱程序能做更强大的事情。你的设备越狱之后，你就能够hook进Apple提供的几乎所有的class，来控制iPhone/iPad的功能。 

[Theos](http://iphonedevwiki.net/index.php/Theos/Getting_Started)大幅简化了编写越狱程序的流程，后面会对该工具进行详细的介绍。

###反汇编工具
[IDA Pro](https://www.hex-rays.com/products/ida/)是一款非常强大的反汇编工具，甚至能够将汇编代码转换成近似于源码的伪代码。

如下图所示[[1]](http://bbs.pediy.com/showthread.php?t=185218)：

![](https://farm6.staticflickr.com/5494/14261323144_b2b81b40a0_o.png)

![](https://farm4.staticflickr.com/3738/14261704475_662169b3ef_o.png)

可以看到，基本上相当于源码。

###调试器
在iOS逆向工程中，可以使用gdb来对iOS应用进行动态分析，进行单步调试，也可以使用[Cycript](http://www.cycript.org/)来对iOS进行动态分析。
<br>
本文简要介绍了iOS逆向工程要用到的工具，后面的文章会对用到的工具做进一步的介绍。

***
[#1 iOS逆向工程简介下的更多文章](http://security.ios-wiki.com/issue-1/)


