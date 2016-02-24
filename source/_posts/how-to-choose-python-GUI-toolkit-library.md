title: 简单明了地选择Python GUI库
date: 2015-12-08 16:40:50
tags:
- Python
- GUI
- PyQT
- Kivy
- wxPython
---
可用于Python GUI程序开发，优秀且更新“跟得上时代”的库有 **wxPython** 、**PyQT**(PySide)、**Kivy**  、**Libavg** ，当然还有 Python 内置的 **Tkinter** 。像 PyGUI 、PyGTK 、PySWT 等，由于上次更新距现在太为久远且文档、学习资料、稳定性等诸多考虑，阿驹认为它们并不适合于开发一个正式的项目，就不作介绍。

## 各种 Python GUI 库简介
### 1. wxPython
* 跨平台，Windows / Unix / unix-like / OS X
* 包装的是 [wxWidgets](http://wxwidgets.org/)，C++写成的
* 完全开源，可以任意用于自己的开源或商业项目
* 不支持 Python3.x （新启了一个名为 Phoenix 项目，支持 Python 3.x，尚未正式发布）
* 学习资料《wxPython in Action》、《wxPython 2.8 Application Development Cookbook》，均有中文版
* 有第三方提供的图形化界面设计工具，但还是有瑕疵，不如PyQT的强大
* 看起来非常本地化地界面
<!--more-->

### 2. PyQT
* 跨平台
* 对QT的包装，QT也是C++实现的
* 多种授权协议可选，若要开发商业软件，需要花钱购买商业授权
* 有自带的功能强大的图形界面设计工具
* 支持Python 2 & 3
* 缺乏成体系的中文学习资料，英文资料足够丰富
* QT 的内存模型和 Python 的内存模型不一致，使用不当可能会导致内存泄露
* 打包后的程序体积较为庞大

### 3. Tkinter
* Python内置GUI库
* 简单易用
* 功能性较 PyQT 和 wxPython 差很多
* 支持Python 2 & 3
* 有第三方提供的图形化界面设计工具
* 有一定量的中文学习资料，英文资料足够丰富
* 看起来不那么本地化地界面

### 4. Libavg
* 跨平台
* 用于以多媒体（图象、文本、音频、视频、数码输出、矢量制图）为主的GUI程序开发高级库（“高级”二字在计算机文档中，通常指相对于“计算机/软件底层”来说的一种抽象概念，“高级”绝不等同于“功能更强，高大上”）
* 支持多点触控、手势识别的系统和设备
* 基于LGPL协议
* 支持Python 2 & 3
* 几乎没有中文学习资料，英文学习资料也较少，就官方文档较为丰富

### 5. Kivy
* 跨平台，除了PC机平台，还支持**Android / iOS / Raspberry Pi**
* 支持多点触控
* 核心基于OpenGL ES2，可利用GPU加速
* 支持Python 2 & 3，不过Python 3的还不那么完善，如果要用于iOS和Android的开发，使用Python 2.7
* 中文学习资料非常少，英文文档足够丰富

## 如何选择GUI库
* 新手，仅仅像尝尝Python GUI编程的乐趣： Tkinter
* 想开发基于PC，功能复杂的商业项目，愿意花钱购买版权： PyQT
* 想开发基于PC，功能复杂的商业项目，不愿意花钱购买版权，考虑Python3支持： Kivy
* 想开发基于PC，功能复杂的商业项目，不愿意花钱购买版权，暂不考虑Python3，要有良好的本地化表现： wxPython
* 想开发基于PC，功能强大，能快速可视化设计的开源软件：PyQT
* 想开发基于PC，功能复杂的软件，阅读英文资料非常吃力： wxPython
* 想开发移动应用，掌上游戏之类的：Kivy
* 想开发丰富的多媒体软件：Libavg
