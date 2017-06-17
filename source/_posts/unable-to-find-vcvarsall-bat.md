title: Unable to find vcvarsall.bat
date: 2016-08-16 05:09:40
tags:
- Python
- Python on Windows
---
## 1. 什么是 vcvarsall.bat ？
Visual Studio 的编译器的一个组件。

## 2. 使用Python开发环境，何时需要它？
在Windows环境下，使用了lxml、mysqlclient、pillow等第三方库，这些第三方库的一些子模块是用C语言扩展写的。而在安装这些库时试图通过源码安装，需要编译，如果当前系统下没有对应的编译环境，则会报错，这里是报“无法找到vcvarsall.bat”。
<!--more-->
## 3. 如何克服没有 vcvarsall.bat 带来的问题？
### 1) 使用已编译的包
推荐下载whl格式的包然后通过 `pip install path_to_package.whl` 命令来安装，如此安装的包还可以被pip管理起来，卸载也方便。可以去这里找编译好的whl包：[http://www.lfd.uci.edu/~gohlke/pythonlibs/](http://www.lfd.uci.edu/~gohlke/pythonlibs/)

### 2) 通过源码编译安装
找不到现成已编译的whl包的情况下，只能通过源码安装。要查清楚各版本Python对应的VC编译环境。可在此处查看Python官方文档的描述：[https://packaging.python.org/extensions/#building-binary-extensions](https://packaging.python.org/extensions/#building-binary-extensions)

也可按照下方提示直接安装相关工具后再编译安装所需的Python包。
* Python2.6 ~ 3.2: [Microsoft Visual C++ Compiler for Python 2.7](https://www.microsoft.com/download/details.aspx?id=44266)
* Python3.3 ~ 3.4: [Windows SDK for Windows 7 and .NET 4.0](https://www.microsoft.com/download/details.aspx?id=8279)
* Python3.5 ~ ?? : [Visual C++ Build Tools 2015](http://go.microsoft.com/fwlink/?LinkId=691126)

根据以上提示安装好相应版本的工具即可，是无需安装 Visual Studio 完整套件的。一个更好的解决办法是，使用 **Linux** 吧 :)
