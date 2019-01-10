
## Flutter在mac上的环境部署

在**[语雀](https://www.yuque.com/xytech/flutter/gs3pnk)**了解了Flutter相关的信息后，博主准备去试试水。

首先进入了Flutter的[官网](https://flutter.io/)。

看到Flutter貌似好像在19年的2月10号会发布1.0的release版本，二话不说。上手部署环境。（博主是Unix的系统）

1. 部署Flutter的环境要下载Flutter的SDK，官网给了例子是下载到root目录的development文件夹。博主选择直接下载到自己的root下面👇

         cd
         >>> 进入/Users/terry
         unzip ~/Downloads/flutter_macos_v1.0.0-stable.zip
         >>> 下载flutter sdk到Download目录下

2. 在bash下配置flutter的全局变量

        export PATH=$PATH:/Users/terry/flutter/bin
        >>> 在 .bash_profile 文件下加入上句话
        source $HOME/.bash_profile
        >>> 重启 .bash_profile 文件
        echo $PATH
        >>> echo 查看是否注入
        -----------------我是分割线------------------
        
        // flutter sdk安装成功后执行
        $ flutter doctor
        // 会对当前环境进行检测 如下所示
        Doctor summary (to see all details, run flutter doctor -v):
        [✓] Flutter (Channel stable, v1.0.0, on Mac OS X 10.14.2 18C54, locale en-CN)
        [✓] Android toolchain - develop for Android devices (Android SDK 28.0.3)
        [✓] iOS toolchain - develop for iOS devices (Xcode 10.1)
        [✓] Android Studio (version 3.2)
        [!] IntelliJ IDEA Ultimate Edition (version 2018.3.1)
            ✗ Flutter plugin not installed; this adds Flutter specific functionality.
            ✗ Dart plugin not installed; this adds Dart specific functionality.
        [✓] VS Code (version 1.30.1)
        [!] Connected device
            ! No devices available
        
        ! Doctor found issues in 2 categories.

    ps： 开始的时候，博主的Android toolchain 和 ios toolchain都是叉叉，具体怎么配置，请往下看。

3. 配置IOS的虚拟环境

首先安装xcode

    sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer

再安装IOS simulator

    open -a Simulator

部署IOS装置

楼主比较懒，写了个shell，把要执行的话都放进去了下面是执行的东西

     brew install --HEAD usbmuxd
     brew link usbmuxd
     brew install --HEAD libimobiledevice
     brew install ideviceinstaller ios-deploy cocoapods
     pod setup

ios的部署在mac上来说还是很傻瓜的，强调一下如果要弄一定要全局翻墙，否则翻车了。。。就不怪我了。

4. 配置Android的虚拟环境

这个地方踩了不少的坑。首先说下，博主用的是vscode,在build的时候因为gradle的机制， 看不到进度，也不出现报错提示。找了好久最后换成Android studio才早到原因。是因为要在Android studio上安装Android的虚拟运行环境才行。下面介绍如何安装。

在Android studio里面打开配置项，找到下列红框提示的内容，然后选择一个版本的Android进行安装

![](/usageImg/Untitled-f322927e-a464-4834-a595-222e4030c961.png)

5. 创建你的第一个Flutter应用

我创建了一个名字叫hello_Flutter的应用，目录结构是这样的。

![](/usageImg/Untitled-dda16a93-2eb9-48da-b1b6-9bf8f6a4d980.png)

如果上述操作都没有问题的话，恭喜你，你已经有了属于你的第一个Flutter项目，并且同时可以在IOS和Android上跑起来了。这一章就先介绍了环境的部署。👌
