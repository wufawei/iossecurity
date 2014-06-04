本文我们将看看如何分析iOS设备上的网络流量。分析应用的网络流量会带来几个方面的好处。它可以帮助我们推断应用是如何管理用户会话的，我们应用调用的另一方是谁，以及应用程序内部是如何工作的等等。我们也会看看如何分析使用SSL的网络流量。

监听网络流量有主动和被动两种方式。如果你对远程分析一个网络中的特定设备的流量感兴趣，那你需要wireshark这个工具。打开Wireshark,开始嗅探网络，添加一个过滤器（fliter，例如 ip.addr == 192.168.1.2）以便它只显示你的设备发出或者接收的网络流量。如果你的无线网卡不够好，那么有些数据包可能会丢失。

如果你想要分析使用SSL的设备的网络流量，有许多方法可以达到目的，比如使用Arpspoof(ARP嗅探) 和SSLStrip的组合。不过，因为我们只对分析某个特定应用的网络流量感兴趣，我们将使用另一个不同的方法。先申明下，本文关注的是分析网络流量而不是劫持网络流量。并且，我们既能够分析通过Wi-Fi的流量，也能分析通过蜂窝网络(cellular)的流量。因为我们只是对分析某个特定应用的网络流量感兴趣，那选择哪种媒介（medium，这里指Wifi或者 celluar)事实上并不重要。

## 使用TCPDump

抓取设备上的网络流量的一个最基本技巧是使用tcpdump。首先，请确保你的设备上安装了tcpdump。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat1.png)

现在，开始在某个特定的接口上抓取数据并且输出到一个文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat2.png)


为了在使用蜂窝数据的时候抓取数据，仅仅需要把上述命令中的接口换成你的蜂窝链接对应的IP地址即可。

为了分析这个文件（抓包保存的文件），你可以把它传输到你的电脑上，然后用Wireshark分析。不过，正如你可能注意到的那样，这个过程确实很繁琐，通过Pipes
可以更好的完成这个过程。更多的信息请看[这][1]。用tcpdump给了我们太多底层的信息，很多信息对我们从分析应用的数据的角度来说并不感兴趣。更好的方法是使用Burpsuite和Snoop-it。


## 使用Snoop-it

我们来看看如何通过Snoop-it来分析网络流量。顺便说一下，如果你还不知道Snoop-it是啥，请查看[这里](http://security.ios-wiki.com/issue-4-6/)。为了查看调用的api和网络请求，
打开在Snoop-it的任意应用然后查看最左边的网络部分。例如，下图展示了Snapchat应用的网络调用情况。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat3.png)


如果我们点击某个特殊请求，我们能够看到请求串的内容 ，比如body等等。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat4.png)


## 使用Burpsuite监听HTTP

再说一下，有许多方法能够查看网络的请求/相应，这其中Burpsuite是一个非常棒工具。可以从它的[官方网站][4]下载。下载它的免费版本就足够完成我们本文的任务了。
顺便说一下，如果你从来没有用过Burpsuite，请查看关于Burpsuite的[另一篇文章][5]。这里最主要的任务就是把Burpsuite当作一个代理，然后路由所有经过它的网络流量。

打开Burpsuite,到Proxy，选择Options

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/073013_1821_IOSApplicat5.png)


点击已经设置的代理，点击编辑（edit），然后选择在Bind to Addrees这个选项的All Interfaces这个选项。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/073013_1821_IOSApplicat6.png)


现在我们能够编辑代理监听的端口，甚至增加一个新的监听代理。Burp有个选项，能够把证书传递给使用SSL的网站。在安装的时候，Burp就默认的创建了一个自签名的CA证书。
现在选中的选项（如下图），“generate CA-signed per-host certificates” 就会用安装Burp的时候创建的CA证书给我们正在连接的host生成一个证书。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/073013_1821_IOSApplicat7.png)


你会被提示一个警告。点击YES。我们选择绑定到所有接口的原因就是我们希望我们的iPhone能够用我们的电脑作为代理，因此选择绑定到本地接口是不够的。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat8.png)


现在，到Proxy－Intercept，然后确保Intercept设置成了off。这是因为你可能不想被每个通过这个代理的数据包打扰。


![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/073013_1821_IOSApplicat9.png)


现在，你可以让你的设备把所有网络流量通过你的代理。在你的iOS设备上，到设置（Settinigs）app，选择Wifi，选择你目前正在连接的网络的设置，滚动到下面，
那里会有一个选项来设置代理。把代理地址设置成正在运行Burpsuite的电脑的IP地址，端口设置为代理运行的端口。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat10.png)


现在代理已经建立起来了，我们已经配置好我们的设备来使用这个代理，打开任意一个不是用SSL的应用（我们将会在本文的后面一点讨论SSL），然后随便用用让应用发出些网络请求。你可以在Burpsuite看到这些请求。下面就是NASA TV app的网络流量。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/073013_1821_IOSApplicat11.png)


使用Burpsuite的好处就是我们能以原生的格式（raw）和十六进制（hex）的格式查看数据包，我们也可以查看参数（Params）和头（Headers）

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/073013_1821_IOSApplicat12.png)



而且，我们也能够看到某个特定请求的对应返回包。


![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/073013_1821_IOSApplicat13.png)


这给了我们详尽的细节来了解一个应用是如何与后台通信的，我们在调用谁，以及请求的格式是什么样的。


## 使用Burpsuite监听HTTPS


不过，上述的技巧对于那些使用HTTPS与后台通信的应用是不起作用的。有些应用只调用SSL连接。例如，如果你试图通过这个代理运行Snapchat就不会成功。不过，有些应用会跳出一个警告，然后让你确认或者取消这个连接。例如，如下图所示就是当通过代理运行Safari的时候的情况。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat14.png)


如果你点击继续，那么你就能够看到应用的网络流量。请注意，这个警告仅仅针对目前的host，如果你浏览到另一个使用HTTPS的网站，那另一个警告又会弹出来，
因为Burpsuite为每个host生成一个假的SSL证书。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/073013_1821_IOSApplicat15.png)


每当我们通过Burpsuite连接一个HTTPS网站的时候，Burp会为每一个host生成SSL证书，这个证书是用我们自己的CA（Certificate Authority）证书签名的。为了确保不让这些警告每次都跳出，我们要在设备上把这个Burp的CA证书设置为受信任的根证书。因此，需要采取下述步骤。首先是得到这个根证书，然后把它安装到设备上。一旦它安装到设备上，它就是一个受信任的根证书它可以签名所有的证书，并且它签名的所有证书都是合法的。请注意这个证书的私钥(private key)保存在你的电脑上，因此当网络流量通过你电脑上的代理的时候，Burp就能够用这个私钥解密这些数据。这个根证书在你把Burp安装到系统的时候就已经被创建好了。

为了把这个根证书安装到你的系统上，首先配置你的浏览器使用Burpsuite 代理。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat16.png)


然后浏览使用SSL的网站，你会看到如下的一个警告

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat17.png)


现在我们的任务就是导出这个用来签名所有这些证书的根证书。对于gmail.com域名，我们不可能导出这个根证书，因为我们不能对gmail的域名添加例外。每个域名都可以实施这样的措施。不过，facebook允许我们添加一个例外。用Firefox访问facebook.com。你会看到一个警告，点击“了解这个风险”（Understanding the Risks）
然后点击添加例外（Add an Exception）

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat18.png)


然后，点击查看（view）

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat19.png)


点击Details标签，然后选择证书层次最上面的证书。这就是根证书。然后点击导出并保存后缀名为.crt的文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat20.png)


你也可以到Burp的文档找到这些步骤。下面就是[这个链接][](http://portswigger.net/burp/help/proxy_options_installingCAcert.html)的屏幕截图。


![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat21.png)


现在你就可以把这个文件发送到你的设备上。使用恰当的社会工程学技巧，攻击者就能够把这个证书安装到设备上，用户不会知道会有什么后果。
下面就是你打开这个证书的时候会得到的警告。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat22.png)


点击安装。你可以看到更详细的警告信息 。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat23.png)


点击完成（done）

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat24.png)


现在，既然这个根证书被认为是合法的，每个被这个根证书签名的证书都会被视为合法的，应用就会允许数据被传输。现在，之前不让我们用假的SSL证书传递数据的Snapchat应用允许我们成功的把数据传输出去。这个网络流量会被Burpsuite拦截。如下图所见，在登录调用的时候，我们可以看到用户名和密码和其他一些这个应用正在调用的API请求。

![](http://resources.infosecinstitute.com/wp-content/uploads/073013_1821_IOSApplicat25.png)

## 总结

本文我们学习了查看通过iOS设备的网络流量的不同方法。能够知道我们调用的对方是哪里，有哪些请求和响应，请求的头和参数是什么等等会帮助我们了解应用内部是如何工作的。


本文原文是 [IOS Application Security Part 11 – Analyzing Network Traffic over HTTP/HTTPS][2]


[1]:http://wiki.wireshark.org/CaptureSetup/Pipes
[2]:http://resources.infosecinstitute.com/ios-application-security-part-11-analyzing-network-traffic-over-httphttps/
[3]:http://wufawei.com/2013/11/ios-application-security-9/
[4]:http://portswigger.net/burp/
[5]:http://resources.infosecinstitute.com/burp-suite-walkthrough/





***
[#6 iOS应用动态分析下的更多文章](http://security.ios-wiki.com/issue-6/)

