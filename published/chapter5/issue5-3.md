
### Keychain 基础

根据苹果的介绍，iOS设备中的Keychain是一个安全的存储容器，可以用来为不同应用保存敏感信息比如用户名，密码，网络密码，认证令牌。苹果自己用keychain来保存Wi-Fi网络密码，VPN凭证等等。它是一个sqlite数据库，位于/private/var/Keychains/keychain-2.db，其保存的所有数据都是加密过的。

开发者通常会希望能够利用操作系统提供的功能来保存凭证（credentials）而不是把它们（凭证）保存到NSUserDefaults,plist文件等地方。保存这些数据的原因是开发者不想用户每次都要登录，因此会把认证信息保存到设备上的某个地方并且在用户再次打开应用的时候用这些数据自动登录。Keychain的信息是存在于每个应用（app）的沙盒之外的。

通过keychain access groups可以在应用之间共享keychain中的数据。要求在保存数据到keychain的时候指定group。把数据保存到keychain的最好方法就是用苹果提供的KeychainItemWrapper。可以到[这][1]下载例子工程。第一步就是创建这个类的实例。

KeychainItemWrapper *wrapper = [[KeychainItemWrapper alloc] initWithIdentifier:@”Password” accessGroup:nil];

标识符（Identifier）在后面我们要从keychain中取数据的时候会用到。如果你想要在应用之间共享信息，那么你需要指定访问组（access group）。有同样的访问组
的应用能够访问同样的keychain信息。

KeychainItemWrapper *wrapper = [[KeychainItemWrapper alloc] initWithIdentifier:@”Account Number” accessGroup:@”YOUR_APP_ID_HERE.com.yourcompany.GenericKeychainSuite”];


要把信息保存到keychain中，使用 setObject:forKey: 方法。在这里， (id)kSecAttrAccount 是一个预先定义好的键（key），我们可以用它来保存账号名称。
kSecClass指定了我们要保存的某类信息，在这里是一个通用的密码。kSecValueData可以被用来保存任意的数据，在这里是一个密码。


[keychainItemWrapper setObject:kSecClassGenericPassword forKey:(id)kSecClass];

[wrapper setObject:@"username" forKey:(id)kSecAttrAccount];

[keychainItemWrapper setObject:@"password"forKey:(id)kSecValueData];

[wrapper setObject:(id)kSecAttrAccessibleAlwaysThisDeviceOnly forKey:(id)kSecAttrAccessible];


kSecAttrAccessiblein变量用来指定这个应用合适需要访问这个数据。我们需要对这个选项特别注意，并且使用最严格的选项。这个键（key）可以设置6种值。

你可以从如下对苹果的[文档][2]的截图看到。

![](http://2we26u4fam7n16rz3a44uhbe1bq2.wpengine.netdna-cdn.com/wp-content/uploads/080513_1630_IOSApplicat1.png)



当然，我们应该绝对不要使用kSecAttrAccessibleAlways。一个安全点的选项是kSecAttrAccessibleWhenUnlocked。这也有些选项是以 ThisDeviceOnly 结尾的。如果选中了这个选项，那么数据就会被以硬件相关的密钥（key）加密，因此不能被传输到或者被其他设备看到。即使它们提供了进一步的安全性，使用它们可能不是一个好主意，除非你有
一个更好的理由不允许数据在备份之间迁移。

要从keychain中获取数据，可以用
NSString *accountName = [wrapper objectForKey:(id)kSecAttrAccount];

###  使用Keychain Dumper导出Keychain中的数据

从Keychain中导出数据的最流行工具是ptoomey3的Keychain dumper。其github地址位于[此][6]。现在到这个地址把它下载下来。然后解压zip文件。在解压的文件夹内，我们感兴趣的文件是keychain_dumper这个二进制文件。一个应用能够访问的keychain数据是通过其entitlements文件指定的。keychain_dumper使用一个自签名文件，带有一个*通配符的entitlments，因此它能够访问keychain中的所有条目。 当然，也有其他方法来使得所有keychain信息都被授权，比如用一个包含所有访问组(access group)的entitlements文件，或者使用一个特定的访问组（access group）使得能够访问所有的keychain数据。
例如，工具[Keychain-viewer][7]就使用如下的entitlements.

com.apple.keystore.access-keychain-keys

com.apple.keystore.device


现在把这个二进制文件上传到你的设备的/tmp文件夹，确保它可执行。


![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat7.png)


现在请确保保存在/private/var/Keychains/keychain-2.db的keychain文件可以被读取。


![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat8.png)



现在，运行这个可执行文件


![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat9.png)



![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat10.png)



如你所见，它可以导出所有的keychain信息。你可以看到许多保存在这里的用户名和密码。例如，你可以看到Mail应用把你账号的用户名和密码保存在keychain中。
类似的，你也可以找到你之前连接过的无线网络的密码和其他更多的信息。上述命令默认只会导出通用和网络密码。你可以通过－h命令查看它的用法。



![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat11.png)





你可以通过 “-a” 命令导出所有数据

![](http://resources.infosecinstitute.com/wp-content/uploads/080513_1630_IOSApplicat12.png)


使得keychain中的数据更安全的一个做法就是使用一个更强的口令。这是因为对某些特定的保护属性（protection attributes）来说，口令会被用作加密keychain中的数据。
iOS默认允许4位数字口令（从0-9999），因此很容易被人在几分钟之内暴力破解。本系列的后续文章中我们会看看暴力破解口令。设置字母加数字的密码会让破解花更多的时间。
一个合适的保护属性（protection attributes）和口令的组合会让keychain的数据更难被获取。


### 小结

在本文中，我们看到了从iOS设备的Keychain中导出数据是多么的容易。虽然在keychain中保存凭证（credentials）和敏感信息比NSUserDefaults和plist文件更安全，但是，
想要破解它也不难。


References

Keychain-Dumper
https://github.com/ptoomey3/Keychain-Dumper

本文原文是 [IOS Application Security Part 12 – Dumping Keychain Data][9]


注：之前我写过一篇文章，[Keychain is not safe][8],大家可以对照着看看，有的应用还是在keychain中保存密码。虽然本文说keychain也容易被破解，不过比NSUserDefaults和plist安全得多，只要我们注意不要在keychain中保存明密码就会在很大程度上提升安全性。



###Keychain is not safe

#### 1 Keychain

 
一般来说mobile app都需要在本地保存一些较为敏感的数据。如何安全的保存这些数据就是一个值得深入探讨的问题。

Mac OS 可以利用Keychain保存各应用中用户的账号密码，让用户不用重复输入，在iOS中也有Keychain，也可以在应用之间共享数据，只是有些限制，用户无法通过手动控制。
要在社保上
KeyChain中的所有数据都以key－value的形式进行存储，可以对其进行add、update、get、delete操作。

如果需要在应用里使用keyChain，需要导入Security.framework，keychain的操作接口声明在头文件SecItem.h里。直接使用SecItem.h里方法操作keychain，需要写的代码较为复杂，可以使用已经封装好了的工具类SFHFKeychainUtils，见：<https://github.com/ldandersen/scifihifi-iphone/tree/master/security> 


对每个应用来说，Keychain都有两个访问区，私有区和公共区。私有区是一个sandbox，本程序存储的任何数据都对其他程序不可见。Keychain中保存的信息是用app unique签名了的，默认只有自己能够访问。



keychain的access group的概念。  

**a)app保持的信息是用一个app unique 签名了的，默认只有自己能够访问**。

" Each application on an iOS device has a unique “application-identifier” that is cryptographically signed into the application before being submitted to the Apple App store.  The keychain service restricts which data an application can access based on this identifier.  By default, applications can only access data associated with their own application-identifier。By default, when no access group is specified, the application will use the unique application-identifier as the access group (thus limiting access to the application itself)"

**b）不同app之间可以通过access_group共享**

app1的group是 app1.accessgroup.item1,

app2在entitlements中加入这个item就可以访问了。

**c） 在不同app之间共享，只能在同一个公司内部的app共享**。  
因为keychain access group 所在的文件entitlements.plist，需要添加到code_sign_entitlements.

这个文件的路径要配置在 Project->build setting->Code Signing Entitlements里，否则公共区无效，配置好后，须用你正式的证书签名编译才可通过，否则xcode会弹框告诉你code signing有问题。所以，苹果限制了你只能同公司的产品共享KeyChain数据，别的公司访问不了你公司产品的KeyChain。


很多app都这样保存用户密码，可是，**这样就安全了吗？**

#### 2 Keychain is not safe

对于没有越狱的设备，上述做法确实很安全。但是，**对于jail break之后的设备，风险就很大了**。

通过上面的分析，我们知道要访问keychain里面的数据，需要


1）和app同样的证书

jail break 相当于对苹果做签名检查的地方打了个补丁，使得不是苹果颁发的证书签名的文件，甚至伪造的签名文件签名的app也能正常使用。所以这个可以轻松绕过。  
2）获得access group的名称  
[Keychain Dumper Updated for iOS 5](http://labs.neohapsis.com/2012/01/25/keychain-dumper-updated-for-ios-5/) 介绍了如何获得acess group。

其实也可以不必获得access group，因为access的匹配是可以用正则表达式的，也就是用*就可以匹配所有group了。
例子如下：


     <dict> <key>keychain-access-groups</key>
     <array> <string>*</string>  </array> </dict></plist>


3）在设备上执行2)中介绍的keychain dumper，就可以得到所有的相关信息。
但是，要在设备上执行keychain dumper，就需要用chmod 777设置其权限，需要root权限，而jail break之后的默认密码是：alpine。


最后可以获得好几个文件，下面是里面的2个例子。（**密码都被我用password字串替换**）

a) 是家里的**WIFI**信息  
![](http://farm9.staticflickr.com/8118/8937210612_75207600a5.jpg)

b）是**某知名微博**

![](http://farm6.staticflickr.com/5446/8937211262_0a3c9a75b6.jpg)

**我在越狱之后的iOS 5.1的iPhone，iPad, iOS 6.1.2的iPad上都测试过，都可以获得如上信息。**
实际中的例子远不止这2个。很多应用都是直接存用户的明文密码的。


#### 3 个人如何防止信息泄露
 a）修改root的默认密码。  
 b) 安装能信任的jail break app。


#### 4 对开发者和公司
 **不要保存用户的明文密码。**  
 **Encryption is a must for sensitive data。**


[1]:http://developer.apple.com/library/ios/
[2]:http://developer.apple.com/library/mac/
[3]:http://wufawei.com/2013/11/ios-application-security-9/
[4]:http://developer.apple.com/library/ios/
[5]:http://wufawei.com/2013/11/ios-application-security-7/ 
[6]:https://github.com/ptoomey3/Keychain-Dumper
[7]:https://code.google.com/p/iphone-dataprotection/wiki/KeychainViewer
[8]:http://wufawei.com/2013/06/Keychain-is-not-safe/ 
[9]:http://resources.infosecinstitute.com/ios-application-security-part-12-dumping-keychain-data/





***
[#5 iOS应用静态分析下的更多文章](http://security.ios-wiki.com/issue-5/)