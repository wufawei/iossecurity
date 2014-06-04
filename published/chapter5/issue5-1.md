## 引言

我们将在本文分析iOS的文件系统，了解其目录是如何组织的，查看一些重要的文件，然后看看如何才能够从数据库文件和plist文件导出数据。我们将学习应用是如何在特定目录（沙盒）内存放数据的，以及怎样才能提取这些数据。

有一个很重要的事情需要注意，在前面的文章中，我们都是以root身份登录进设备的。设备上有另一个用户名叫mobile, 一个mobile用户拥有的权限是少于root用户的。除了Cydia和少数的应用以root权限应用之外，其他应用都是以mobile的身份应用的。有些苹果内部的daemon服务也以root权限运行。执行ps aux就可以查看清楚。在最左边，我们可以看到用户列。可以看到Cydia以root身份运行，所有其他应用都以mobile身份运行，
例如/Applications/AppStore.app/AppStore，有些demon也以root身份运行，如/usr/sbin/wifid。一些你通过Cydia安装的应用也可能会以root身份运行。
一旦你的设备一越狱，默认root和mobile的密码都是alpine.

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat1.png)


可以配置一个应用以root权限运行。你可以看看Stack Overflow上的[这篇文章][1]来了解更多细节。


我们ssh进设备。到/Applications目录。你可以在该文件夹下看到一些应用。它们中的大多是都是iOS预装的，有些应用是通过Cydia安装的，比如Ternimal 应用。请注意，所有运行在/Applications的应用并不运行在沙盒环境，而所有在/var/mobile/Applications目录下的应用都运行在一个沙盒环境下。文章后面会讨论沙盒。不过，它们默认依然以mobile用户运行，除非专门做了配置。


![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/072913_1331_IOSApplicat2.png)

所有从App Store下载的应用都位于/var/mobile/Applications目录。这个目录也包含用installipa或者其他外部源如Cydia安装的应用。所有这些应用都运行在沙盒环境下。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat3.png)


请注意，从iOS4及以后，每个应用都驻留的环境叫做沙盒(Sandbox)。这样做的主要目的是不允许应用访问它自己沙盒外的任何数据。这样做会更安全。不过，应用用合适的权限是可以访问用户某些特定的用户数据的。例如要用户允许去访问联系人，照片等等。不过，对这些也有不少争论。
例如从iOS6开始，应用在得到用户允许之后才能访问用户的联系人。在这之前，应用不需要获得任何权限就能访问用户的联系人，这导致了较大的[争议][2]，例如Path应用。

通过使用Entitlements，你可以访问沙盒外的好些东西。你可以读读这里的[文档][3]。例如，要获得一个用户的calender的读权限，.entitlements文件中的entitlement key com.apple.security.personal-information.calendars必须标志为YES。

让我们看看某个特定应用的目录结构。首先到Snapchat的目录看看。对于所有应用都是类似的结构。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat4.png)



  * Snapchat.app(应用名称.app)文件夹包含所有的资源文件(images)，plist文件和应用的二进制文件。
  * Documents目录用于存放任意文件。相对于应用文件来说，这提供了一个只能在应用内访问的单独目录。下面是从[苹果文档][4]中摘录的一句话。

"把用户数据放到/Documents/。用户数据是你的应用不能再创建的任意数据，比如用户的文档或者任何其它用户产生的内容“。

* tmp文件夹用于存放用户的临时数据。应用的开发者有责任释放被改文件夹占有的内存.
* Library文件夹可以用来保存那些不是用户数据的文件。



  你可以从下面的[苹果文档][5]的截图知道更多信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat5.png)


##从数据库中收集信息


苹果用sqlite数据库存了很多信息。这些数据库数据库通常以.db或者.sqlitedb结尾。对于开发者来说，许多功能比如Core Data， NSUserDefaults都从一个较低的层次操作这些sqlite数据库。可以从这些数据库抽取出特定应用，甚至操作系统级别的许多信息。可能包括电话历史或者应用内保存的邮件等等。要找到所有的.db文件，
可以用命令 find . -name *.db

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat6.png)

你可以得到在设备上保存的所有数据库文件。让我们先看下其中的一些重要数据库文件。

我在设备上安装了gmail应用。下面这个文件对我来说看起来很有趣。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat7.png)

看起来这个文件包含了一些重要的信息。让我们先用sqlite客户端分析一下这个文件。请注意，你需要在你的设备上安装sqlite客户端，比如sqlite3。
我们先打开设备，然后用命令sqlite3 file_name打开数据库文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat8.png)

请注意，你会得到一个sqlite解释器。让我们打开headers，这样我们就可以看到所有的列表名称。你可以用.tables命令看看数据库存放的所有表。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat9.png)

有些表看起来很有趣，比如cached_contacts, cached_queries 和 cached_messages。让我们从cached_messages导出所有信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat10.png)


正如我们看到的那样，导出了所有缓存了的邮件。

类似的，我们也可以导出所有的短信（SMS）数据库，位于/private/var/mobile/Library/SMS.你可以从导出文件中看到一条消息，
其文本是Test message for ios security tutorial

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat11.png)


另一个例子是联系人数据库，其位置是 /var/mobile/Library/AddressBook

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat12.png)



你也可以查看电话历史记录，位于 /private/var/wireless/Library/CallHistory

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat13.png)



有时候用命令行做这些事情确实很费时间。一个更好的分析方法就是导出这些信息到你电脑上，（然后用工具打开）。例如，
下载 Address Book Sqlite database

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat14.png)

我们可以用GUI Sqlite客户端工具来分析这个文件。我这里用的是MesaSQLite。免费且易用。
在MesaSQLite中，先到File，然后点击Open Database，选择db文件，然后在Content tab，选择一个表然后点击查看所有（Show All）

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat15.png)


如你所见，许多信息都可以从这些数据库文件中获取。我推荐你自己去探索下，找找其他应用甚至操作系统的数据库文件看看。

<br>
##从plist文件中获取信息

plist是用户存放许多不同设置和配置的结构化文本文件。因为这些信息都是以key-value这种键值对来存放信息的，所以要改变这些信息非常容易。因此，许多开发者有时会在这些文件中存放许多不该存放在这的信息。

即使在一个没有越狱的设备上，plist文件也可以通过工具iExplorer获取。你可以用iExlorer看看plist文件。例如如下图是一个Defcon iOS应用的plist
中存放的信息。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat16.png)


下图是Snapchat应用的Documents目录中保存的plist文件截图。第一个高亮的区块实际上是特定用户的认证标识(authentication token)，第二个高亮的区块是
Snapchat的用户名称。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat17.png)


Plist文件也可能包含机密信息，比如用户名和密码。**有一个事情需要特别注意的就是，任何人都可以从设备中导出plist文件，即使这个设备没有越狱。
你也可以从用户的iTunes 备份中导出这些plist文件。过去数年，有开发者把机密数据存放在plist文件中，这是不正确的做法。**
Linkedin iOS应用被发现的一个漏洞就是它把用户的认证信息存放在plist文件中，你可以到[这][7]找到更多信息。

如果你想在terminal看这些plist文件，你可以先用工具plutil把它转化为xml格式，命令是  plutil -covert xml1 [filename]。首先用下面的2个命令找到设备上所有的plist文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat18.png)

然后把它转成xml格式

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat19.png)


现在文件是结构化的格式了，我们可以用vim打开它。

![](http://resources.infosecinstitute.com/wp-content/uploads/072913_1331_IOSApplicat20.png)

如你所见，我们现在能够分析plist文件的内容。

<br>
##总结

在本文中，我们查看了iOS的文件系统，学习到其目录结果是如何组织的，查看了一些重要文件，并且学习了如何从数据库和plist文件中导出重要数据。


<br/>
本文原文是 [IOS Application Security Part 10 – IOS Filesystem and Forensics][6]


[1]: http://stackoverflow.com/questions/7841344/gaining-root-permissions-on-ios-for-nsfilemanager-jailbreak/8796556#8796556
[2]:http://arstechnica.com/gadgets/2012/02/path-addresses-privacy-controversy-but-social-apps-remain-a-risk-to-users/
[3]:http://developer.apple.com/library/ios/
[4]:https://developer.apple.com/library/mac/navigation/
[5]:http://developer.apple.com/library/mac/
[6]:http://resources.infosecinstitute.com/ios-application-security-part-10-ios-filesystem-and-forensics/
[7]:http://blog.scoopz.com/2012/04/07/linkedin-ios-app-also-vulnerable-to-plist-identity-theft/



***
[#5 iOS应用静态分析下的更多文章](http://security.ios-wiki.com/issue-5/)