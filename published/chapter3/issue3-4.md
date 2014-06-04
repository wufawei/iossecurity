###Reveal简介
Reveal是分析iOS应用UI的利器：


> Reveal能够在运行时调试和修改iOS应用程序。它能连接到应用程序，并允许开发者编辑各种用户界面参数，这反过来会立即反应在程序的UI上。就像用FireBug调试HTML页面一样，在不需要重写代码、重新构建和重新部署应用程序的情况下就能够调试和修改iOS用户界面。--[InfoQ](http://www.infoq.com/cn/news/2013/07/debug_ios_apps_with_reveal)

Reveal运行在Mac上，目前的最新版本是1.0.4，可以从这里[下载](http://revealapp.com/)，要求Mac OS X 10.8及以上，iOS 6以及以上。

现在正式版本可以下载试用30天，试用期后需要购买。有需要的话可以买一个，功能相当强大。


###Reveal的功能

####查看iOS应用的View层次结构
使用Reveal的效果如图：
![](https://d262ilb51hltx0.cloudfront.net/max/1271/1*gh0UVZG3WXePXxHDDhyiww.png)

图中最左侧可以看到View的名称，中间是View的3D展示效果，可以非常清楚的看到View的层次结构。



####修改参数后无需编译即可看到效果

Reveal另一个非常有用的功能就是动态修改参数，无需编辑动态查看效果。如下图所示：

![](https://farm4.staticflickr.com/3771/14088254130_585420251e_c.jpg)

显示出UILabel和UIView的关键属性值，更重要的是，这些都可以动态修改，比如我们改成**测试**，如下图：
![](https://farm4.staticflickr.com/3762/14251740006_5a351487c8_c.jpg)

把其中的图片的宽度从80改成50，改之后的效果如图：

![](https://farm4.staticflickr.com/3743/14088214209_05c1b3cb1d_c.jpg)


下图箭头所指向的对方都可以动态修改。

![](https://farm4.staticflickr.com/3833/14295039713_0b6d6c3465_c.jpg)


关于Reveal的更多功能，欢迎大家去探索并分享。下面介绍Reveal如何集成到iOS应用中去调试。


###Reveal的3种加载方法

####加载方法（1）  
下载Reveal之后打开，在菜单中的Help中可以找到集成到Xcode项目的方法，这里不再赘述。 

####加载方法（2）  
  
[Integrating Reveal without modifying your Xcode project][1]   
[reveal: 檢視 iOS app 的 view 結構][2]。  
给出了如何不用修改Xcode工程就可以加载使用Reveal的方法。

在当前用户目录新建一个文件.lldbinit，位于~/.lldbinit，LLDB每次启动的时候都会加载这个文件。

在.lldbinit中输入如下内容：

    command alias reveal_load_sim expr (void*)dlopen("/Applications/Reveal.app/Contents/SharedSupport/iOS-Libraries/libReveal.dylib", 0x2);
    
    command alias reveal_load_dev expr (void*)dlopen([(NSString*)[(NSBundle*)[NSBundle mainBundle] pathForResource:@"libReveal" ofType:@"dylib"] cStringUsingEncoding:0x4], 0x2);
    
    command alias reveal_start expr (void)[(NSNotificationCenter*)[NSNotificationCenter defaultCenter] postNotificationName:@"IBARevealRequestStart" object:nil];
    
    command alias reveal_stop expr (void)[(NSNotificationCenter*)[NSNotificationCenter defaultCenter] postNotificationName:@"IBARevealRequestStop" object:nil];
    
    
    
上述文件创建了4个命令：

    reveal_load_sim,reveal_load_dev, reveal_start 和 reveal_stop
    
    
    
  > * `reveal_load_sim` 这个只在iOS模拟器上有效。它从Reveal的应用程序bundle中找到并加载libReveal.dylib（请确保你把Reveal安装到了系统的Application文件夹，如果你换地方了，你修改上述的文件）。
    
  > * `reveal_load_dev` 这个命令在iOS设备和模拟器上都有效。**不过，它需要你在Build Phase中的的Copy Bundle Resources中加上libReveal.dylib，请确保没有放到Link Binary With Libraries这个地方**。
    
   > * `reveal_start` 这个命令发出一个通知启动Reveal Server。

   > * `reveal_stop` 这个命令发出一个通知停止Reveal Server。
   
请注意：只有在iOS应用发出了UIApplicationDidFinishLaunchingNotification通知之后，比如应用的delegate已经处理过application::didFinishLaunchingWithOptions:之后才能调用上面的`reveal_load_*`命令，然后再调用reveal_start

在设备起来之后，你就可以断下应用，在LLDB提示框中输入上述的命令了。

![](https://farm4.staticflickr.com/3805/14263611406_5dd7df0f35_z.jpg)

上述的过程还需要手动输入，下面介绍如何设置条件断点，使得Reveal在启动之后自动加载。

在你的应用的application:didFinishLaunchingWithOptions 中的代码出加一个断点，然后右键，选择编辑断点。

输入如下图一样的命令：

![](https://farm6.staticflickr.com/5239/14100108060_cc36dbb783_z.jpg)

重新运行下应用，如果控制台输出了如下信息：

     Reveal server started.
     
说明Reveal已经自动成功加载。
     

 
####加载方法（3）  

[Reveal查看任意app的高级技巧][4]介绍了如何在越狱设备上查看任意app的技巧：

 * iOS设备需要越狱，iOS6以上
 * 安装Reveal，越狱设备与安装Reveal的Mac在同一wifi内。
 * 点击菜单Help / Show Reveal Library in Finder，获取libReveal.dylib
 * 将libReveal.dylib上传到设备的/Library/MobileSubstrate/DynamicLibraries
 * 编辑并上传一个libReveal.plist，格式和/Library/MobileSubstrate/DynamicLibraries下面的其他plist类似，其中的filter的bundle写要查看的iOS App的bundle Id。
格式如下：

 { Filter = { Bundles = ( "你要查看的app的bundle Id" ); }; }
 
 * 重启iOS设备






[1]:http://blog.ittybittyapps.com/blog/2013/11/07/integrating-reveal-without-modifying-your-xcode-project/
[2]:http://adison.logdown.com/posts/159350-reveal-check-the-ios-app-view-structure
[3]:http://blog.ittybittyapps.com/blog/2013/09/26/reveal-shortcuts-and-tips/
[4]:http://c.blog.sina.com.cn/profile.php?blogid=cb8a22ea89000gtw





###小结

本文简要介绍了Reveal的功能和几种加载方法。欢迎大家去体验下Reveal的强大功能。最后，可以看看关于Reveal的tips:[Reveal tips: Navigation][3]。















***
[#3 Mac上需要安装的工具下的更多文章](http://security.ios-wiki.com/issue-3/)