title: MacOSX10.9.4下安装PIL库失败的解决方法
date: 2014-07-11 10:59:59
tags:
  - Python
  - PIL
  - Pillow
---
## 安装PIL库
在Mavericks10.9下安装PIL库失败，这是一个python的图形处理库。错误提示如下：
```
jus-iMac:~ ju$ sudo pip install PIL
Password:
Downloading/unpacking PIL
  Could not find any downloads that satisfy the requirement PIL
    Some externally hosted files were ignored (use --allow-external PIL to allow).
Cleaning up...
No distributions at all found for PIL
Storing debug log for failure in /Users/ju/Library/Logs/pip.log
```
<!--more-->
再根据错误提示，使用如下命令安装：
```
sudo pip install PIL --allow-external PIL --allow-unverified PIL
```

仍然出错。但这次错误提示变了：
```
In file included from _imagingtk.c:19:
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/usr/include/tk.h:78:11: fatal error: 'X11/Xlib.h' file not found
#       include <X11/Xlib.h>
                ^
1 error generated.
error: command 'cc' failed with exit status 1
```

安装原版PIL解决办法如下：
```
sudo ln -s /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/System/Library/Frameworks/Tk.framework/Versions/8.5/Headers/X11 /usr/local/include/X11
export CFLAGS=-Qunused-arguments
export CPPFLAGS=-Qunused-arguments
sudo pip install PIL --allow-external PIL --allow-unverified PIL
```

## 使用Pillow代替PIL
因为PIL已经很久没更新，而且对Python3不支持。Pillow是从PIL fork的一个分支，更新及时，对Python2/3支持都很友好。完全兼容PIL，可以成为PIL的代替品。
安装步骤如下：
```
$ brew install libtiff libjpeg webp little-cms2
$ pip install Pillow
```
