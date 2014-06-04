##引言

在前面的文章中我们介绍了如何用class-dump-z来导出iOS应用的类信息，如何利用Cycript挂钩进程、执行运行时操纵和method swizzling，用gdb分析app的流程。
然而，可能有更好的方式能够做这些事。如果能够有一个工具能够做所有这些事情并且能够更好的展示这些信息就太好了。

Snoop-it就是这样一个tool。它允许我们进行运行时分析和对iOS应用进行黑盒安全评估。它提供一个非常简洁的web界面。在写本文的时候，Snoop-it还没正式发布，我给作者写了邮件，
他们非常友好的提供给我一个beta版本做测试。你可以到它的[官网][1]查看或者你可以在[Twitter][2]上关注作者。

Snoop-it提供的功能可以从对其[官方地址][1]的截图看到。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat1.png)

##安装
（备注：目前已经可以通过在Cydia上添加源、然后直接下载安装了。） 
要安装Snoop-it到你的设备上。你不得不下载deb包，然后用sftp上传到你的设备上。在命令行下用命令dpkg -i [packageName]来安装Snoop-it到你的设备上。
一旦安装完成，重启你的设备。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/071813_1302_IOSApplicat2.png)


一旦安装完成，你会看到Snoop-it的图标，点击它，你可以看到如下的界面。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat3.png)

到设置中按你所需配置。在这里，我们选择端口为12345，并且关闭验证。如果你所在的网络有许多其他用户，或者比较调皮的用户，建议你还是开启验证。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat4.png)

现在，用Snoop-it提供的地址打开Snoop-it的Web界面。在我这，地址是：http://10.0.1.79:12345

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat5.png)

你将看到这个Web界面。如果你读一下，会发现它让你在Snoop-it中选择你要分析的应用，在应用中打开要分析的应用，然后刷新这个Web界面。现在回到
Snoop-it，选择我们要分析的应用，在我这，我将要选择MethodSwizzlingDemo应用，和上一篇文章用的应用一样。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat6.png)

请确保要分析的应用已经打开并保持在前台，现在刷新Snoop-it的Web界面。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat7.png)


正如你看到的，现在你有一个很漂亮的界面，现在你可以对这个应用进行详尽的安全评估了。

<br>
##分析

在左边，在Analysis下面，点击Objective-C Classes。在右边你就看到所有的类信息，比如属性和方法名称。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat8.png)

橘子色的代表有实例的类。例如，当你把鼠标从ViewController的类上面移动的时候，你会看到一个类实例的信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat9.png)

类似的，那可以看到AppDelegate的方法和属性。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat10.png)


回到View Controller上面来，我们可以通过Snoop-it调用方法。点击右上角的 Setup and Invoke。正如我们在上一篇文章中提到的那样，使用这个方法，我们能够绕过这个应用
的验证。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat11.png)

选择对应的实例（这里只有1个实例，但是如果view controller被复用，那么就可能会有多个实例），点击Invoke Method.

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat12.png)

这样我们就调用了对应的方法，并且绕过了程序的验证。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat13.png)


Snoop-it的另一个牛逼功能是我们可以切换到任意的view controller。例如，在左边的Analysis下面，选择View Controller，选择右边的view controller，然后点击Display Controller.
你就能够切换到那个view controller。你也可以根据这个View Controller是否在另一个View controller上面来决定点击Close/Hide View Controller。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat14.png)


你能够通过点击 Reset display返回。这个功能能够让我们把view controller与对应的view关联起来。我太喜欢Snoop-it的这个功能了。

<br>
##运行时修改


Snoop-it支持多种运行时修改，包括修改你的硬件标识符比如Mac地址，UDID，设备模型号等等。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat15.png)

你还可以弄个假地址。这个对于那些利用GeoEncrytion来保护它们数据的应用来说 非常有用。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat16.png)


而且，你还可以跟踪方法和系统调用的流程。请注意，你需要每隔几秒在方法调用后点击最上面的Refresh刷新一下。请注意，因为我们在beta版本上测试，可能作者会改变这个行为使得我们不必每隔几秒就点击刷新按钮。对有些用户来说，这些信息可能太多，但是对于像我一样已经开发过多年iOS程序的人来说这些信息是相当简单直接的。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat17.png)

##监控
Snoop-it允许你查看哪些文件和目录目前正被应用访问。为了达到这个目的，请点击Monitoring下面的 Filesystem.这个功能特别有用，尤其是当应用正在往db写数据的时候，这个功能能够让你找出db文件的名字。你也可以双击它们，然后下载到你的机器上再分析。

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat18.png)

你也可以看到应用调用的敏感API。比如在地址簿查找信息，访问camera，或者访问设备的UDID。下面是预装的App Store应用访问的敏感API

![](http://resources.infosecinstitute.com/wp-content/uploads/071813_1302_IOSApplicat19.png)


我们也可以看到这个应用存在keychain的所有信息。它也会列出所有通过NSURLConnection访问的HTTP请求。这两个功能都可以在monitoring下面看到。我把这些功能留给读者你去尝试。
我们将在另一篇文章中介绍如何从keychain中dump出数据。

你会很高兴的知道Snoop-it有公共的API，所以我们能够利用它来编写自动化测试或者编写我们自己的用户界面。关于XML-RPC web service API的文档可以在[官网][3]找到。

##总结

本文我们学习了如何使用Snoop-it来进行运行时分析和对iOS apps进行黑盒安全评估。 Snoop-it离发布还有几个星期的时间，尽管你可以像我一样向作者发邮件索要beta版本。
有一个我特别想要Snoop-it添加的功能就是执行Method Swizzling。我确信Snoop-it对于任何对iOS应用的安全分析感兴趣的人都是一个好工具，并且它会变得越来越好。

（注：已经发布了，可以在Cydia上下载，需要先添加源，具体参见其官方网站）

本文原文是 [http://wufawei.com/2013/11/ios-application-security-9/](http:/wufawei.com/2013/11/ios-application-security-9/)

[1]:https://code.google.com/p/snoop-it/
[2]:http://twitter.com/aykay/
[3]:http://code.google.com/p/snoop-it/wiki/
[4]:http://resources.infosecinstitute.com/ios-application-security-part-9-analyzing-security-of-ios-applications-using-snoop-it/



***
[#4 iOS设备上的工具下的更多文章](http://security.ios-wiki.com/issue-4/)