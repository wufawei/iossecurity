本文我们将看看应用在本地存储数据有哪些方法以及这些不同方法的安全性。

我们将会在一个demo上这些这些测试，你可以从[github][1]上下载这个例子程序。对于CoreData的例子，你可以从[这][2]下载例子程序。本例有一个不同点就是我们将会在模拟器上运行这些应用，而不是在设备上运行。这样做的目的是为了证明在前面文章中的操作都可以通过Xcode来把这些应用运行在模拟器上。当然，你也可以把这应用安装到设备上。

<br>
## NSUserDefaults

保存用户信息和属性的一个非常普通的方法就是使用NSUserDefaults。保存在NSUserDefaults中的信息在你的应用关闭后再次打开之后依然存在。保存信息到NSUserDefaults的一个例子就是保存用户是否已登录的状态。我们把用户的登录状态保存到NSUserDefaults以便用户关闭应用再次打开应用的时候，应用能够从NSUserDefaults获取数据，根据用户是否登录展示不同的界面。有些应用也用这个功能来保存机密数据，比如用户的访问令牌，以便下次应用登录的时候，它们能够使用这个令牌来再次认证用户。


从[github][1]可以下载例子应用，运行起来。你可以得到下面的界面，现在输入一些信息到与NSUserDefaults相关的文本框，然后点击下面的“Save in NSUserDefaults”。这样数据就保存到NSUserDefaults了。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat1.png)

许多人不知道的是保存到NSUserDefaults的数据并没有加密，因此可以很容易的从应用的包中看到。NSUserDefaults被存在一个以应用的bundle id为名称的plist文件中。
首先，我们需要找到我们应用的bundle id。因为我们在模拟器上运行，我们可以在/Users/$username/Library/Application Support/iPhone Simulator/$ios version of simulator/Applications/找到应用。我这的路径是：“Users/prateekgianchandani/Library/Application Support/iPhone Simulator/6.1/Applications”。

一旦我们找到那个目录，我们可以看到一堆应用。我们可以用最近修改的日期找到我们的应用，因为它是最近修改的。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat2.png)

进入到应用的bundle里面。通过NSUserDefaults保存的数据都可以在如下图所示的Library -> Preferences -> $AppBundleId.plist文件中找到。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat3.png)

打开这个plist文件，我们可以清楚的看到这个文件的内容。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat4.png)

有时候，plist文件会以二进制格式保存，因此可能第一下看到会觉得不可读。你可以用plutil工具把它转成xml格式，或者直接用iExplorer在设备上查看。

<br>
## Plist 文件

另一种保存数据普遍用的方法就是plist文件。**Plist文件应该始终被用来保存那些非机密的文件，因为它们没有加密，因此即使在非越狱的设备上也非常容易被获取**。已经有[漏洞][5]被爆出来，大公司把机密数据比如访问令牌，用户名和密码保存到plist文件中。在下面的demo中，我们输入一些信息并保存到plist文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat5.png)

下面是把数据保存到plist文件的代码。

    [plain]
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory,NSUserDomainMask,YES);
    NSString *documentsDirectory = [paths objectAtIndex:0];
    NSString *filePath = [documentsDirectory stringByAppendingString:@"/userInfo.plist"];
    NSMutableDictionary* plist = [[NSMutableDictionary alloc] init];
    [plist setValue:self.usernameTextField.text forKey:@"username"];
    [plist setValue:self.passwordTextField.text forKey:@"passwprd"];
    [plist writeToFile:filePath atomically:YES];
    [/plain]

如你所见，我们能够给plist文件指定路径。我们可以搜索整个应用的所有plist文件。在这里，我们找到一个叫做userinfo.plist的文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat6.png)

可以看到，它包含了我们刚刚输入的用户名/密码的组合。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat7.png)

<br>
## CoreData和Sqlite文件

因为CoreData内部使用Sqlite来保存信息，因此我们这里将只会介绍下CoreData。如果你不知道什么是CoreData，下面是从苹果文档介绍CoreData截的图。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat8.png)

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat9.png)

因此，基本上，CoreData可以用来创建一个model，管理不同对象的关系，把数据保存到本地，然后当你查询的时候从本地缓存中获取它们。本例中，我们将使用一个demo，位于[github][6]。运行起来，你会发现它只是一个简单的RSS feed。


![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat10.png)


这个应用用CoreData保存数据。一个非常重要的一点就是CoreData内部使用sql，因此所有文件都以.db文件保存。我们到这个app的bundle中去看看。
在这个app的bundle中，你可以看到那里有一个MyCoreData.sqlite的文件。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat11.png)

我们可以用sqlite3分析。我这slite文件的地址是：~/Library/Application Support/iPhone Simulator/6.1/Applications/51038055-3CEC-4D90-98B8-A70BF12C7E9D/Documents.


![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat12.png)

我们可以看到，这里有个叫做ZSTORIES的表。在Core Data中，每个表名开头都会被追加一个Z。这意味着真正的实体名称是STORIES，如我们在工程的源码文件看到的那样。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat13.png)

我们可以非常容易的导出这个表的所有值。请却表headers的状态是on。

![](http://resources.infosecinstitute.com/wp-content/uploads/101613_1214_IOSApplicat14.png)


正如我们看到的那样，默认的，保存在CoreData的数据都是没有加密的，因此可以轻易的被取出。因此，我们不应该用CoreData保存机密数据。
有些库包装了一下CoreData, 声称能够保存加密数据。也有些库能够把数据加密保存到设备上，不过不使用CoreData。例如，Salesforce Mobile SDK
就使用了一个被称为[SmartStore][7]的功能来把加密数据以"Soups"的形式保存到设备上。

<br>
## Keychain

有些开发者不太喜欢把数据保存到Keychain中，因为实现起来不那么直观。**不过，把信息保存到Keychain中可能是非越狱设备上最安全的一种保存数据的方式了**。**而在越狱设备上，[没有任何事情][8]是安全的。**[这篇文章][9]展示了使用一个简单的wrapper类，把数据保存到keychain是多么的简单。使用这个wrapper来保存数据到keychain就像把数据保存到NSUserDefaults那么简单。下面就是一段把字符串保存到keychain的代码。请注意和使用NSUserDefaults的语法非常类似。

    [plain]
    PDKeychainBindings *bindings = [PDKeychainBindings sharedKeychainBindings];
    [bindings setObject:@"XYZ" forKey:@"authToken"];
    [/plain]
    下面是一段从keychain中取数据的代码。
    
    [plain]
    PDKeychainBindings *bindings = [PDKeychainBindings sharedKeychainBindings];
    NSLog(@"Auth token is %@",[bindings objectForKey:@"authToken"]]);
    [/plain]


<br>
## 一些小技巧


正如之前讨论过的那样，没有任何信息在越狱设备上是安全的。攻击者能够拿到Plist文件，导出整个keychain，[替换][10]方法实现，并且攻击者能做他想做的任何事情。**不过开发者能够使用一些小技巧来使得脚本小子从应用获得信息变得更难。**比如把**文件加密**放到本地设备上。[这里][11]这篇文章详细的讨论了这一点。或者你可以**使得攻击者更难理解你的信息**。比如考虑要把某个用户的认证令牌（authentication token）保存到keychain当中，脚本小子可能就会导出keychain中的这个数据，然后试图劫持用户的会话。我们只需再把这个认证令牌字符串**反转**一下（reverse），然后再保存到keychain中，那么攻击者就不太可能会知道认证令牌是反转保存的。当然，攻击者可以追踪你的应用的每一个调用，然后理解到这一点，但是，**一个如此简单的技术就能够让脚本小子猜足够的时间，以至于他们会开始寻找其它应用的漏洞**。另一个**简单技巧**就是在每个真正的值保存之前**都追加一个常量字符串**。


本文原文 [IOS Application Security Part 20 – Local Data Storage (NSUserDefaults, CoreData, Sqlite, Plist files)][12]



[1]:https://github.com/prateek147/localDataStorageDemo
[2]:https://github.com/ChrisDrit/Core-Data-Example-Code
[3]:http://highaltitudehacks.com/security/
[4]:http://wufawei.com/2013/11/ios-application-security-7/
[5]:http://garethwright.com/facebook-mobile-ecurity-hole-allows-identity-theft/
[6]:https://github.com/ChrisDrit/Core-Data-Example-Code
[7]:http://www.modelmetrics.com/tomgersic/storing-data-offline-with-salesforce-mobile-sdk-smartstore/
[8]:http://wufawei.com/2013/11/ios-application-security-12/
[9]:http://highaltitudehacks.com/2013/09/17/ios-dev-storing-info-in-keychain-with-nsuserdefaults-like-syntax
[10]:http://wufawei.com/2013/11/ios-application-security-8/
[11]:http://highaltitudehacks.com/2013/09/26/ios-dev-encrypted-images-and-saving-them-in-app-sandbox
[12]:http://resources.infosecinstitute.com/ios-application-security-part-20-local-data-storage-nsuserdefaults-coredata-sqlite-plist-files/




***
[#5 iOS应用静态分析下的更多文章](http://security.ios-wiki.com/issue-5/)