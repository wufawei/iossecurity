####确定目标
第一步是确定目标，即你要分析的App，你需要在这个App上编写Tweak完成的功能，比如挂钩SpringBoard使得桌面启动的时候弹框，或者拦截某个具体的应用的特定API调用，获得关键信息。

####导出头文件
确定目标之后，就可以利用Clutch先破解App，然后利用class-dump-z导出头文件，找到你感兴趣的类，对它进行分析。

####获得类的方法
有时候，头文件没有所有方法调用的信息，这个时候你可以利用cycript，使用之前介绍的[trick](http://security.ios-wiki.com/issue-4-5/)，比如printMethod打印出所有的方法。

####编写Tweak
这一步你应该拿到需要Hook的类以及对应的方法，利用[Tweak编写简介]（http://security.ios-wiki.com/issue-7-2/）中介绍的技术编写Tweak。

####安装与测试
把上一步的Tweak安装到你的设备上，验证你的Tweak是否工作正常。


####小结
这里简要介绍了Tweak编写的一般步骤，后面的章节会有更具体的例子来介绍这个步骤。


***
[#7 iOS越狱程序编写下的更多文章](http://security.ios-wiki.com/issue-7/)