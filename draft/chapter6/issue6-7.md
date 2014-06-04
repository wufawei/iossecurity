本文我们将看看如何使用Introspy对iOS应用进行黑盒测试。Introspy由[ISEC partners][1]开发，其github地址在[这][2]。Introspy由两个单独的模块组成，一个追踪器，一个分析器。它是分析iOS应用程序安全毫无疑问的最强大工具之一。

第一步就是在你的设备上安装Introspy追踪器。你可以在[这][3]下载到其deb包。下载成功之后，上传并安装到你的设备上。下图展示了上面提到的步骤需要执行的操作。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat1.png)

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat2.png)

一旦追踪器安装好了，重启你的设备。到设置应用，你会看到一个关于Introspy的不同区块。
现在
![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat3.png)

Introspy App区块允许你选择想要分析的应用。因此，点击它，然后选择你想要分析的应用。我这里选择了Path应用来做分析。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat4.png)


现在到Introspy的Settings，确保每个选项都已选中，特别是选项Log to The Console（把日志输出到控制台）。如果我们选中
这个选项，Introspy分析器将会把它找到的关于这个应用（app）所有信息都输出到控制台，这样我们就能在运行时看到这些信息。


![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat5.png)

一旦选中了Path应用，请确保它没有在运行。如果它正在运行，请退出并重启Path。另外，请确保你的设备和你的电脑连接好了，因为我们想要看Introspy
分析器记录的日志。同时，请大家你机器上的Xcode（如果你在Mac上），到Window-> Organizer->Devices。在左边的菜单选择你的设备，然后选择控制台。
现在你就可以看到你的设备的日志。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat6.png)

现在开启Path应用，然后尽可能多的使用这个应用。同时，Introspy分将会在后台运行，并且会尽可能多的收集关于这个应用的信息。你也可以看到设备的日志。
这里，我们可以看到有一个向server发起的请求，我们可以看到这个请求的所有内容，包括路径和请求参数。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat7.png)

并且现在，你可以看到这个应用正在使用NSUserDefaults来验证userId这个键。这个信息其实应该保存在keychain的。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat8.png)

但是最有趣的的信息可以从下面的图中看到。正如你所见，这个应用使用NSUserDefaults来验证HangTracerEnabled这个布尔值。这个可能是
用来看这个应用是否在运行时被分析，如果是的话，就退出。不过，这技巧看起来失败了，因为它没有能够检测到Introspy分析器。
不过当我用Snoop-it分析Path应用的时候，它crash了。所以，这个布尔值确定无疑的是用来看应用是否正追踪（被分析）。我们将会在随后的
文章中介绍这些概念。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat9.png)

除了在控制台展示这个应用的运行时信息，Introspy也能够把它保存到你设备上的一个sqlite数据库中。从你的电脑上，你可以获取这个数据库文件
并且Introspy会把它转换成可展示的格式。要从你的iOS 设备上获取这个数据库，首先你需要github页面下载[Introspy][4]。到这个分析器的目录，
然后使用如下图的命令。你需要指定在你本地机器上要把报告保存的位置，同时也要指定你的iOS设备的IP地址。


![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat9.png)


如你所见，Introspy会要求你选择一个数据库文件。这些数据库文件是为每个我们在Settings中选择的应用创建的。在这里，我们选择为Path应用创建的
数据库。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat10.png)

你可以看到，这个数据库被保存在当前目录下面，同时，当前目录下有一个叫做Path-Report的文件夹被创建。如果我们进入那个文件夹，并且
打开report.html，下图就是我们将会看到的内容。如你所见，Introspy已经用一个很不错的方法把全部信息都展示出来了。我们可以看到被追踪的调用
和其参数。。


![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat12.png)







我们也可以看到其中有一列叫做"Potential Findings"。这些都是Introspy认为存在漏洞的地方。在这里，我们将看看存储数据不安全的问题。
这可能不算是一个漏洞，因为保存的信息不一定非常重要。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat13.png)

你也可以像下图那样，选择某些选项来定制你看到的信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat14.png)

例如，我已经把它配置成只显示关于UserPreferences的方法。这个信息可能会非常有用，因为它可以帮我们找出那些可能被写入NSUserDefaults的一些重要信息。
即使没有在下图中显示，我也能够容易的知道Path把我的用户id（userId）保存到NSUserDefaults，并且在很多地方都会用到（这个用户id）。这个信息理应保存在
更安全的地方，比如，keychain。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat15.png)

我们也可以直接从命令行对保存的数据库文件进行分析。下面是使用信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat16.png)

让我们给这个命令传递参数http'。如你所见，它导出了一列通信方的列表。

![](http://resources.infosecinstitute.com/wp-content/uploads/091713_1309_IOSApplicat17.png)


Introspy也可以被其他Python脚本导入。我们也可以增加签名来标志漏洞或者不安全的配置。我们将在随后的文章中作介绍。


<br>
## 总结


本文我们查看了如何使用Introspy对iOS应用进行黑盒测试。Introspy由两个模块组成，
一个追踪器，一个分析器。我们可以用追踪器来对应用执行运行时分析。追踪器会把信息保存到sqlite文件中以便后续用分析器分析，追踪器也可以把所有信息都输出到设备的控制台上。分析器可以用这个数据库文件生成一个详尽的HTML报告。



References

Introspy

https://github.com/iSECPartners/introspy


本文原文是 [iOS应用程序安全(17)-使用Introspy对iOS应用进行黑盒测试](http://wufawei.com/2013/11/ios-application-security-17/)


[1]:https://www.isecpartners.com/
[2]:https://github.com/iSECPartners/introspy
[3]:https://www.dropbox.com/s/z5cwqk5wti3zsvd/com.isecpartners.introspy-v0.3-iOS_6.1.deb?dl=1
[4]:https://github.com/iSECPartners/introspy/archive/master.zip
[5]:http://resources.infosecinstitute.com/ios-app-security-part-17-black-box-assess-ios-apps-using-introspy/




***
[#6 iOS应用动态分析下的更多文章](http://security.ios-wiki.com/issue-6/)


