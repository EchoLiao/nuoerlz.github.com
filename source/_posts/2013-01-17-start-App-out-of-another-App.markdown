---
layout: post
title: "iOS中在程序中调用打开第三方程序"
date: 2013-01-17 17:24
comments: true
categories: iOS
---


## 在某一程序里调用打开其它程序

原则上iOS的沙箱原理, 是阻止一个app去访问其他app的资源乃至是系统底层的资源的.
但是我们可以通过一种变相的方式: 通过对应的URL模式和其他程序进行通讯. 为了实现
这种程序间的通信, 需要对 被调用程序(A) 作一些修改.

<!--more-->


### 配置被调用程序(A)

为了使自己可以被其它程序能够调用打开, 要作的配置:

#### 注册定制的URL模式

在为您的应用程序注册URL类型时, 必须指定CFBundleURLTypes属性的子属性:

* CFBundleURLName 这是个字符串, 表示URL类型的抽象名. 为了确保其唯一性, 建议您
  使用反向DNS风格的标识, 比如 com.yixia.VPlayer

* CFBundleURLSchemes 这是个URL模式的数组, 表示归属于这个URL类型的URL. 每个模式
  都是一个字符串. 如 VPlayer

修改 Info.plist ,

```diff
    $ git diff VPlayer-Info.plist
        diff --git a/VPlayer-Info.plist b/VPlayer-Info.plist
        index faba046..b60a0b9 100644
        --- a/VPlayer-Info.plist
        +++ b/VPlayer-Info.plist
        @@ -2,6 +2,21 @@
         <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
         <plist version="1.0">
         <dict>
        +    <key>CFBundleGetInfoString</key>
        +    <string></string>
        +    <key>CFBundleURLTypes</key>
        +    <array>
        +        <dict>
        +            <key>CFBundleURLSchemes</key>
        +            <array>
        +                <string>VPlayer</string>
        +            </array>
        +        </dict>
        +        <dict>
        +            <key>CFBundleURLName</key>
        +            <string>com.yixia.VPlayer</string>
        +        </dict>
        +    </array>
                <key>CFBundleDevelopmentRegion</key>
                <string>English</string>
                <key>CFBundleDisplayName</key>
```


### 处理URL请求

应用程序委托(UIApplicationDelegate)在 application:handleOpenURL: 方法中处理传
递给应用程序的URL请求

下面代码实现了这个委托方法:
```objc
    - (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url
    {
        if ([[url scheme] isEqualToString:@"VPlayer"]) {
            // get it!
            nslog(@"url: %@", url);
            return YES;
        }
        return NO;
    }
```


### 在其它程序中调用测试

用 openURL: 方法调用

```objc
    - (IBAction)openAnotherApp:(id)sender
    {
        NSString *video = @"VPlayer://http://v.youku.com/v_show/id_XNDk3ODAzMzEy.html";
        [[UIApplication sharedApplication] openURL:[NSURL URLWithString:video]];
    }
```


### 其它常见的调用系统应用的方法

```objc
    // app store
    NSURL *appStoreUrl = [NSURL URLWithString:@"http://phobos.apple.com/WebObjects/MZStore.woa/wa/viewSoftware?id=291586600&amp;mt=8"];
    [[UIApplication sharedApplication] openURL:appStoreUrl];

    // message
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"sms:55555"]];

    // phone
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"tel://8004664411"]];

    // mail
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"mailto://info@iphonedevelopertips.com"]];

    // google map
    NSString *searchQuery = @"the postion I want to know";
    searchQuery = [searchQuery stringByAddingPercentEscapesUsingEncoding: NSUTF8StringEncoding];
    NSString *urlString = [NSString stringWithFormat:@"http://maps.google.com/maps?q=%@", searchQuery];
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString: urlString]];

    // browser
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"http://www.google.com/"]];
```


###  公开你的 Scheme

有一个网站提供了很多公开了的URL scheme, 你也可以将自己的程序的URL scheme提交上
去, 公开出来.

[URL scheme 网站地址][rs1]  
[提交你的 url scheme 的地址][rs2]  


### Reference

[IOS在当前程序中调用任意的第三方程序][ra1]  
[iOS 如何在一个app中调用另一个app][ra2]  
[Shared Interapp Communication!][ra3]  
[Core Foundation Keys][ra4]  
[IPhone URL Schemes][ra5]  

[ra1]:http://blog.sina.com.cn/s/blog_6ae8b50d0100v4u3.html
[ra2]:http://blog.csdn.net/sql_help/article/details/7299553
[ra3]:http://www.handleopenurl.com/
[ra4]:https://developer.apple.com/library/ios/#documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html
[ra5]:http://wiki.akosma.com/IPhone_URL_Schemes
[rs1]:http://www.handleopenurl.com/
[rs2]:http://www.handleopenurl.com/developers


## 使 iOS 应用支持 "Open in ..." 特性

"Open in ..."[特性][op1]是指把某一文件用另外的应用来查看若处理, 比如, 邮件附件
, 当你点击它时, 就会出现一个"Open in ..."按钮, 点击, 其就会列出可以处理该类型
附件的应用, 然后你就可以选择任意一个打开该附件了.

文件类型处理是iphone os 3.2新增的, 与已经存在的自定义URL方案是不同的. 你能注册
你的应用程序来处理特定的文件类型并且任何应用程序 使用文档控制器能手动处理这些
文档在你的应用程序中.


### 向系统注册

为了使用"Open in ...", 你需要向系统注册, 告知系统你的应用能够处理的文件类型.
这通过配置文件 Info.plist 配置实现.

其中, 关键字 CFBundleIconFiles 用来设置你的应用的图标; CFBundleDocumentTypes
是最重要的节点, 在子节点 LSItemContentTypes 中列出应用所支持的文件类型, 这些文
件类型的名字用的是UTI(Uniform Type Identifiers)标准, 在[这里][op2]查看系统已定义
好的名字; 当然你也可定制一个[新的文件类型的UTI][op3].

```diff
    $ git diff VPlayer-Info.plist
        diff --git a/VPlayer-Info.plist b/VPlayer-Info.plist
        index b60a0b9..3afdfb8 100644
        --- a/VPlayer-Info.plist
        +++ b/VPlayer-Info.plist
        @@ -2,6 +2,36 @@
         <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
         <plist version="1.0">
         <dict>
        +    <key>CFBundleIconFile</key>
        +    <string></string>
        +    <key>CFBundleIconFiles</key>
        +    <array>
        +        <string>Icon.png</string>
        +        <string>Icon@2x.png</string>
        +        <string>Icon-72.png</string>
        +        <string>Icon-Small-50.png</string>
        +        <string>Icon-Small.png</string>
        +        <string>Icon-Small@2x.png</string>
        +    </array>
        +    <key>CFBundleDocumentTypes</key>
        +    <array>
        +        <dict>
        +            <key>CFBundleTypeName</key>
        +            <string>VPlayer support File</string>
        +            <key>CFBundleTypeRole</key>
        +            <string>Viewer</string>
        +            <key>LSHandlerRank</key>
        +            <string>Default</string>
        +            <key>LSItemContentTypes</key>
        +            <array>
        +                <string>public.audiovisual-content</string>
        +                <string>public.url</string>
        +                <string>public.file-url</string>
        +                <string>public.url-name</string>
        +                <string>public.image</string>
        +            </array>
        +        </dict>
        +    </array>
             <key>CFBundleGetInfoString</key>
             <string></string>
```

在这里, 我把我的应用配置成可以处理音视频, 图象, URL.


### 处理URL请求

当应用被"Open in ..."调用打开时, 会收到系统发送的URL, 该URL指向要被处理的文件
或文件的复本, 所以在程序里, 也必须作相应的处理. 很幸运, 这
与***在某一程序里调用打开其它程序--处理URL请求***是一样的. 参见文章的前面部分.
因为两者是一样处理流程, 所以有一点要注意, 可以用下面的方法把两者区分开:

```objc
    - (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url
    {
        if([url isFileURL]) {
            // Handle file being passed in ("Open in ...")
        } else {
            // Handle custom URL scheme
        }
    }
```


### Reference

[Registering the File Types Your App Supports][or1]  
[Previewing and Opening Files][or2]  
[Declaring New Uniform Type Identifiers][or3]  
[Adopting Uniform Type Identifiers][or4]  
[System-Declared Uniform Type Identifiers][or5]  
[Register File Types to Associate File Extension with an App][or6]  
[Is the 'Open In' Feature in iOS Limited to 10 Apps?][or7]  
[UTI iPhone支持依文件后缀名打开应用][orx]  


[or1]:https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html
[or2]:http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/PreviewingandOpeningItems.html
[or3]:http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html
[or4]:http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/understanding_utis/understand_utis.tasks/understand_utis_tasks.html
[or5]:http://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html
[or6]:http://ios.biomsoft.com/2012/04/03/register-file-types-to-associate-file-extension-with-an-app/
[or7]:http://ipadinsight.com/ios/is-the-open-in-feature-in-ios-limited-to-10-apps/
[orx]:http://blog.csdn.net/zaitianaoxiang/article/details/6658492

[op1]:http://ios.biomsoft.com/2012/04/03/register-file-types-to-associate-file-extension-with-an-app/
[op2]:http://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html
[op3]:http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html


hhhh
