####简介

[Logify](http://iphonedevwiki.net/index.php/Logify)能够接受一个.h头文件作为输入，然后输出.xm文件（MobileSubstrate扩展），这个.xm文件hook这个类的所有方法，当这些方法被调用的时候打印log。这有助于你发现哪些方法被调用了。Logify在安装了Theos之后就有。

####用法

在命令行下输入类似的命令：

    /opt/theos/bin/logify.pl MomoLocationManager.h > tweak.xm
    
其中MomoLocationManager.h是头文件，后面的tweak.xm是自动生成的tweak文件。

在我的mac上我是这样输入的：

    ZPs-MBP:momoLocation admin$ /opt/theos/bin/logify.pl MomoLocationManager.h > tweak.xm


这个tweak.xm的内容如下：


    %hook MomoLocationManager
    - (void)setFakeLocation:(CLLocation* )fakeLocation { %log; %orig; }
    - (CLLocation* )fakeLocation { %log; CLLocation*  r = %orig; NSLog(@" = %@", r); return r; }
    - (void)setReviseLocation:(CLLocation* )reviseLocation { %log; %orig; }
    - (CLLocation* )reviseLocation { %log; CLLocation*  r = %orig; NSLog(@" = %@", r); return r; }
    - (void)setLocation:(CLLocation* )location { %log; %orig; }
    - (CLLocation* )location { %log; CLLocation*  r = %orig; NSLog(@" = %@", r); return r; }
    - (void)setLastLocTime:(NSDate* )lastLocTime { %log; %orig; }
    - (NSDate* )lastLocTime { %log; NSDate*  r = %orig; NSLog(@" = %@", r); return r; }
    - (void)setLocManager:(CLLocationManager* )locManager { %log; %orig; }
    - (CLLocationManager* )locManager { %log; CLLocationManager*  r = %orig; NSLog(@" = %@", r); return r; }
    - (void)setBeginDate:(NSDate* )beginDate { %log; %orig; }
    - (NSDate* )beginDate { %log; NSDate*  r = %orig; NSLog(@" = %@", r); return r; }
    +(id)shareMomoLocationManager { %log; id r = %orig; NSLog(@" = %@", r); return r; }
    -(id)distanceBetweenLocationDictionary:(id)dictionary { %log; id r = %orig; NSLog(@" = %@", r); return r; }
    -(BOOL)isOriginLocationValid { %log; BOOL r = %orig; NSLog(@" = %d", r); return r; }
    -(BOOL)isReviseLocationValid { %log; BOOL r = %orig; NSLog(@" = %d", r); return r; }
    -(void)locationManager:(id)manager didFailWithError:(id)error { %log; %orig; }
    -(void)locationManager:(id)manager didUpdateToLocation:(id)location fromLocation:(id)location3 { %log; %orig; }
    -(void)refreshLocationIfExceedLimit { %log; %orig; }
    -(void)HandleTimer { %log; %orig; }
    -(void)updateServerLocation { %log; %orig; }
    -(void)locationFail { %log; %orig; }
    -(void)locationFinish { %log; %orig; }
    -(void)updateSelfLocation:(id)location { %log; %orig; }
    -(void)cancelLocation { %log; %orig; }
    -(void)reviseLocationToError:(id)error { %log; %orig; }
    -(void)reviseLocationToFail:(id)fail { %log; %orig; }
    -(void)reviseLocationToSuccess:(id)success { %log; %orig; }
    -(void)reviseLocationTo { %log; %orig; }
    -(void)stoplocation { %log; %orig; }
    -(void)locationTimeOut { %log; %orig; }
    -(void)starLocationAndCorrectLocation:(BOOL)location { %log; %orig; }
    -(id)getLatestLocationWithInterval:(double)interval { %log; id r = %orig; NSLog(@" = %@", r); return r; }
    -(void)dealloc { %log; %orig; }
    -(id)init { %log; id r = %orig; NSLog(@" = %@", r); return r; }
    %end
    
    
使用[Theos：iOS越狱程序开发框架](http://security.ios-wiki.com/issue-3-6/)介绍的方法编写Tweak，然后用上述生成的tweak.xm覆盖自动生成的tweak.xm文件，然后安装到设备上。

打开Xcode的Organizer看设备的log。可以得到如下的log信息：

![](https://farm6.staticflickr.com/5040/14304842946_43350e8afb_b.jpg)


我们可以发现有哪些方法被调用了，其中红色划线的地方，就是我们的当前位置的经纬度。

我的经纬度被上报了。


从这个图中，可以发现最终是调用了：


   - (void)setLocation:(CLLocation* )location
   
   
在下一节，我们将拦截这个方法，给这个方法传递一个假地址，看看是否能达到目的。




***
[#8 修改某陌生人交友软件的位置信息下的更多文章](http://security.ios-wiki.com/issue-8/)