####简介

Charles是Mac下常用的对网络流量进行分析的工具，类似于Windows下的[Fiddler](http://www.telerik.com/fiddler)。在开发iOS程序的时候，往往需要调试客户端和服务器的API接口，这个时候就可以用Charles，Charles能够拦截SSL请求、模拟慢速网络、支持修改网络请求包并多次发送、能够篡改Request和Response等强大的功能。下面介绍安装和使用方法。

####下载安装

可以从这里[下载](http://www.charlesproxy.com/download/)Charles，有30天的试用期。

安装后打开Charles，试用版本会延迟10秒后加载。如下图：
![](https://farm4.staticflickr.com/3821/14266035281_fa910f691d_z.jpg)


第一次运行时会弹出如下提示，点击**Grant Privileges**即可。

![](https://farm4.staticflickr.com/3791/14289524533_97ed46d95d_z.jpg)








然后打开菜单**Proxy**的**Proxy Setting**，设置端口为：**8888**。
如下图所示：

![](https://farm4.staticflickr.com/3750/14261806452_e3e2954ec6_c.jpg)

![](https://farm4.staticflickr.com/3760/14077329790_5e89a82847_c.jpg)


####支持拦截SSL请求
根据官方的[文档](http://www.charlesproxy.com/documentation/faqs/ssl-connections-from-within-iphone-applications/):


######真实设备上的设置方法

如果要在真实设备上拦截SSL连接，需要安装证书，从[http://charlesproxy.com/charles.crt](http://charlesproxy.com/charles.crt)下载即可。

下载证书之后，双击它，会有如下提示：

![](https://farm4.staticflickr.com/3802/14082751570_1b5bc3d8d7_c.jpg)

点击其中的“**Always Trust**”，然后输入自己的账号密码给它授权。

可以从Keychain中查看授权之后的结果：
![](https://farm4.staticflickr.com/3794/14082704678_98d7144bb2_c.jpg)


######模拟器上的设置方法

下载[http://www.charlesproxy.com/assets/install-charles-ca-cert-for-iphone-simulator.zip](http://www.charlesproxy.com/assets/install-charles-ca-cert-for-iphone-simulator.zip)，解压之后双击，就可以在模拟器上拦截SSL请求了，甚至都不用重启模拟器。






<br>




####设置iOS设备的代理
首先获取运行Charles的Mac的IP地址，可以打开命令行（Terminal）输入**ifconfig**得到IP地址。

或者spotlight 打开 Network Utility，如下图所示：

![](https://farm4.staticflickr.com/3719/14078584917_a33f4ffaaa.jpg)

然后在iOS设备的WiFi网络中设置代理，如下图：

![](https://farm6.staticflickr.com/5072/14263016912_b0b5586618.jpg)

确保移动设备和Mac在同一个WIFI下面。

####通过Charles分析iOS设备的网络请求

按下Record按钮，或者Proxy菜单里面的Start Recording即可对iOS设备的网络请求进行监听。


####Charles的功能

> * 拦截SSL请求
> * 模拟慢速网络  
    菜单**Proxy**中的Throttle Setting可以对此进行设置
> * 支持修改网络请求包并多次发送
> * 断点功能  
  Charles能够断到发送请求前（篡改Request）和请求后（篡改Response）
> * 捕获记录控制
   可以过滤出关注的请求。菜单**Proxy**中的Record Setting可以对此进行设置
   
暂时不展开介绍这些功能，这里介绍再多都比不上自己运行几分钟的效果好。


####小结

Charles还有很多强大的功能等待大家挖掘，欢迎大家使用下体验下这些功能。
  





***
[#3 Mac上需要安装的工具下的更多文章](http://security.ios-wiki.com/issue-3/)
