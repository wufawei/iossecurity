iOS程序类型分为3类：Application, Dynamic Library，后台Daemon。

在越狱的设备上才会遇到需要开发后面两种类型程序的情况。

###Application
平时我们开发提交到App Store的应用即是Application，设备没有越狱的情况下，应用只能访问沙盒内存文件和数据。

###Dynamic Library
Dynamic Library(动态链接库)，在其他平台很常见，比如Windows平台的DLL。苹果官方做了限制，所以在非越狱的情况下，需要提交到App Store的应用是不能包含动态链接库的，否则无法通过审核（Review）

后面要介绍的越狱程序（Tweak）开发，就是动态链接库。我们开发的大部分越狱程序，都是编译成动态链接库，然后通过越狱平台的[MobileSubstrate](http://iphonedevwiki.net/index.php/MobileSubstrate)（iOS7上叫[CydiaSubstrate](http://www.cydiasubstrate.com/)）来加载进入目标程序（Target），通过对目标程序的挂钩（Hook），来实现相应的功能。

后面会详细介绍越狱程序开发的原理，会对这个细节做进一步的介绍。

###后台Daemon
后台Daemon类似于Windows的Service。对于Application来说，切换到Home就会暂停运行，而Daemon会在后台运行。在越狱设备上，之前用来拦截垃圾短信和电话的工具都是运行在后台的Daemon。


***
[#2 iOS文件系统及程序类型下的更多文章](http://security.ios-wiki.com/issue-2/)
