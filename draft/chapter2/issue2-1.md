出于安全考虑，iOS系统把每个应用以及数据都放到一个沙盒（sandbox）里面，应用只能访问自己沙盒目录里面的文件、网络资源等（也有例外，比如系统通讯录、照相机、照片等能在用户授权的情况下被第三方应用访问）[[1]](https://developer.apple.com/library/ios/documentation/iphone/conceptual/iphoneosprogrammingguide/TheiOSEnvironment/TheiOSEnvironment.html)。


请注意，使用沙盒的目的是为了防止被攻击的应用危害到系统或者其他应用，它并不能阻止应用本身被攻击，因此，开发者需要防御式的编程来避免应用被攻击。苹果官方是这样说的：

> Important: The purpose of a sandbox is to limit the damage that a
> compromised app can cause to the system. Sandboxes do not prevent
> attacks from happening to a particular app and it is still your
> responsibility to code defensively to prevent attacks. For example, if
> your app does not validate user input and there is an exploitable
> buffer overflow in your input-handling code, an attacker could still
> hijack your app or cause it to crash. The sandbox only prevents the
> hijacked app from affecting other apps and other parts of the system.


为了便于应用组织数据，每个沙盒内都有几个名字固定的子目录用来保存文件，下图是沙盒的目录结构：

![](https://farm4.staticflickr.com/3671/14260129612_02cfb70304.jpg)

主要有4个目录[[2]](https://developer.apple.com/library/mac/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)：

> * MyApp.app  
该目录包含了应用程序本身的数据，程序打包的时候的资源文件和一些本地文件就是存放在这个目录下的。
程序的可执行程序、plist文件也在这个目录下。  
这个目录不会被iTunes同步

> * Documents
使用这个目录来保存关键数据。关键数据指那些应用不可再生的数据。  
这个目录会被iTunes同步

> * Library
用来保存一些配置文件和其他一些文件。其中使用NSUserDefaults写的设置数据都会保存到Library/Preferences目录下的一个plist文件中。Library/Caches可以用来保存可再生的数据，比如网络请求，用户需要负责删除对应文件。  
这个目录（除了Library/Caches外）会被iTunes同步
> * tmp  
使用这个目录来保存各种应用下次启动不再需要的临时文件。当应用不再需要这些文件的时候，需要主动将其删除。（当应用不再运行的时候，系统可能会将此目录清空。）  
这个目录不会被iTunes同步


<br>
###获取主要目录路径的方式

####沙盒目录
    NSLog(@"%@",NSHomeDirectory());

####MyApp.app
    NSLog(@"%@",[[NSBundle mainBundle] bundlePath]);

####tmp
   
    NSLog(@"%@",NSTemporaryDirectory());
    
####Documents

    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    NSString *docPath = [paths objectAtIndex:0];
    NSLog(@"%@",docPath);
    
####Library
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSLibraryDirectory, NSUserDomainMask, YES);
    NSString *libPath = [paths objectAtIndex:0];
    NSLog(@"%@",libPath);

***
[#2 iOS文件系统及程序类型下的更多文章](http://security.ios-wiki.com/issue-2/)
