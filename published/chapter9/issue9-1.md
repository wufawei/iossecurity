
在[5.2 本地数据存储及安全性](http://security.ios-wiki.com/issue-5-2/)这一节，我们对本地数据存储对安全性做了详尽的分析。

NSUserDefaults，plist,sqlite3等等，即使设备不越狱，攻击也能够提取出数据。在设备越狱之后，keychain中的数据也不安全。

因此，要对敏感数据加密，且尽量保存到keychain中（比如token信息）。


下面是2个例子。（**密码都被我用password字串替换**）

a) 家里的**WIFI**信息  
![](http://farm9.staticflickr.com/8118/8937210612_75207600a5.jpg)

b）**某知名微博**

![](http://farm6.staticflickr.com/5446/8937211262_0a3c9a75b6.jpg)

**我在越狱之后的iOS 5.1的iPhone，iPad, iOS 6.1.2的iPad上都测试过，都可以获得如上信息。**


实际中的例子远不止这2个。很多应用都是直接存用户的明文密码的。

####个人如何防止信息泄露
 a）修改root的默认密码。  
 b) 安装能信任的jail break app。


####对开发者和公司
 **不要保存用户的明文密码。**  
 **Encryption is a must for sensitive data。**

考虑到用户的安全，这里并没有点名某个具体的应用，做移动App，一定要考虑本地数据存储的安全问题。

大家可以拿感兴趣的App，用本系列文章介绍的方法具体分析一下。

***
[#9 本地数据和网络通信下的更多文章](http://security.ios-wiki.com/issue-9/)
