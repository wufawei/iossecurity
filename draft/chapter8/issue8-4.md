根据上一节的分析：我们最终编写的Tweak程序如下：

    #import <CoreLocation/CoreLocation.h>
    
    %hook MomoLocationManager
    
    - (void)setLocation:(CLLocation* )location { %log; CLLocation *location1 = [[CLLocation alloc] initWithLatitude:39.91276257 longitude:116.36980966];%orig(location1); }
    
    %end
    
    
其中的经纬度(39.91276257, 116.36980966)，如下图所示，是我随意填写的。

![](https://farm6.staticflickr.com/5570/14141578997_853d8cbc9a_b.jpg)



使用[Theos：iOS越狱程序开发框架](http://security.ios-wiki.com/issue-3-6/)介绍的方法编写Tweak，然后安装到设备上。

最终的效果如下图所示：

![](https://farm3.staticflickr.com/2899/14324468961_aa08320ca6_c.jpg)


成功把当前位置替换。

利用本系列文章介绍的工具可以做很多有意思的事情，欢迎读者自己去找寻目标并体验这些技术。

***
[#8 修改某陌生人交友软件的位置信息下的更多文章](http://security.ios-wiki.com/issue-8/)