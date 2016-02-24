title: 修复pip升级后pip与pip3命令均安装三方包至python3目录下
date: 2015-11-26 17:27:10
tags:
  - Python
  - Linux
---
## 状况描述
目前安装好较新的 Linux 发行版后，系统默认已经安装好了 python2 与 python3 。有时，也需要在这两种 python 环境下方便地安装python包。而根据 [pip的安装文档](https://pip.pypa.io/en/stable/installing/) 安装好了pip以后，默认情况下是安装在python2下面的（因为Linux默认的python版本是2）。

然而我们再执行 `python3 get-pip.py` 来为 python3 装好pip后，我们发现如下情况：
```
$ pip -V
pip 7.1.2 from /usr/local/lib/python3.4/dist-packages (python 3.4)
$ pip3 -V
pip 7.1.2 from /usr/local/lib/python3.4/dist-packages (python 3.4)
$ pip2 -V
pip 7.1.2 from /usr/local/lib/python2.7/dist-packages (python 2.7)
```
<!--more-->
`pip` 与 `pip3` 都是在 python3 目录下，那么执行 `pip install packagename` 和 `pip3 install packagename` 都会把 package 安装至 python3 的环境中去。

## 解决办法
使用如下命令重新安装一次pip：
```
sudo pip2 install --upgrade --force-reinstall pip
```

结果如下：
```
$ pip -V
pip 7.1.2 from /usr/local/lib/python2.7/dist-packages (python 2.7)
$ pip3 -V
pip 7.1.2 from /usr/local/lib/python3.4/dist-packages (python 3.4)
```

## 开始就该更轻松
```
$ sudo apt-get install python3-pip
$ sudo apt-get install python-pip
```
按照以上命令安装好pip以后，自然应该是pip对应在python2目录下，pip3对应在python3下的。注意先安装3的，后安装2的，因为这种方式安装pip的时候，后安装的都会将先安装的 `pip` 覆盖掉。不过这种方式安装的pip不一定是最新稳定版本的。需要自己再更新。如果不慎更新除了问题，参考上面的解决方式。

## 最轻松的方式
请一开始就使用 `pyenv` 和 `virtualenv` 工具来为不同的项目生成不同的python版本及相互隔离的虚拟环境。这样每个项目的python版本及其依赖的第三方包都不相关联。

参考[用pyenv 和 virtualenv 搭建单机多版本python 虚拟开发环境](http://www.cnblogs.com/npumenglei/p/3719412.html)
