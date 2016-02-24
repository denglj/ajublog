title: Python编码最佳之最佳实践
date: 2015-10-09 11:30:00
tags: Python
---
***

## 总体的
### 价值观
* 把你为自己构建的工具贡献给别人
* 简单性通常比功能性更好
* 解决90%的情况，忽略唱反调的
* 优雅胜于丑陋
* 构建开源代码（哪怕是为了闭源项目）

### 通用开发准则
* 显式优于隐式
* 重视可读性
* 任何人都可以解决任何问题
* 一旦破窗（糟糕的设计、错误的决定、丑陋的代码）被发现就尽快修复它们
    <!--more-->
    > 注：[破窗效应](https://zh.wikipedia.org/zh-cn/%E7%A0%B4%E7%AA%97%E6%95%88%E5%BA%94)
* 立即做比永不做好
* 严格测试，为每个新功能/特性写好文档
* 更重要的是测试驱动开发--人为驱动开发
* 这些准则很可能会变化

## 特别的
### 风格
遵循[PEP8](http://www.python.org/dev/peps/pep-0008/)是明智的。
#### 命名
* 变量名、函数名、方法名、包名、模块名
  + `lower_case_with_underscores` （带下划线小写字母）
* 类名、异常名
  + `CapWords` （单词首字母大写）
* 保护的方法名和内部函数名
  + `_single_leading_underscore(self, ...)` （单下划线开始）
* 私有方法名
  + `__double_leading_underscore(self, ...)` （双下划线开始）
* 常量名
  + `ALL_CAPS_WITH_UNDERSCORES` （全大写带下划线）

##### 通用命名准则
尽量避免一个字符的变量名，尤其是l,I,o,O,0等不易区分的。
例外：在非常短的代码块中，能从直接的上下文中清晰明了的知道变量含义。

**适用情况**
```Python
for e in elements:
    e.mutate()
```

避免多余的标签：
**可以的**
```Python
import audio

core = audio.Core()
controller = audio.Controller()
```

**不可以**
```Python
import audio

core = audio.AudioCore()
controller = audio.AudioController()
```

使用“反向符号”：
**可以的**
```Python
elements = ...
elements_active = ...
elements_defunct = ...
```
**不可以**
```Python
elements = ...
active_elements = ...
defunct_elements ...
```

避免使用setter和getter方法：
**可以的**
```Python
person.age = 42
```
**不可以**
```Python
person.set_age(42)
```

#### 缩进
四个空格，永远不要用制表符tab，说的够多了。

#### 导入
导入整个模块而不是单个变量。例如顶级模块`canteen`中有`canteen/sessions.py`.

**可以的**
```Python
import canteen
import canteen.sessions
from canteen import sessions
```
**不可以**
```Python
from canteen import get_user  # Symbol from canteen/__init__.py
from canteen.sessions import get_session  # Symbol from canteen/sessions.py
```
例外：一些文档中特别注明的应当导入单个变量的第三方代码
基本准则：避免循环引用。参考[这里](https://sites.google.com/a/khanacademy.org/forge/for-developers/styleguide/python#TOC-Imports)。

所有的导入分三个部分放在代码的最上方，每个部分用一个空行分隔，放置顺序：
1. 系统/标准库导入（例如 import math）
2. 第三方库/代码导入（例如 import flask）
3. 本地/本项目导入 （例如 import my_module）

基本准则：能清楚地知道每个模块来自哪里

#### 文档
遵循[PEP 257](http://www.python.org/dev/peps/pep-0257/)文档字串指南。[reStructured Text](http://docutils.sourceforge.net/docs/user/rst/quickref.html) 和 [Sphinx](http://sphinx-doc.org/) 可以帮助强制执行这些标准。

单行文档字符串用于简单的函数：
```Python
"""返回 ``foo`` 的路径名称。"""
```

多行文档字符串应该包括：
* 概括的说明
* 用例
* 参数
* 返回类型和语义

```Python
"""Train a model to classify Foos and Bars.

Usage::

    >>> import klassify
    >>> data = [("green", "foo"), ("orange", "bar")]
    >>> classifier = klassify.train(data)

:param train_data: A list of tuples of the form ``(color, label)``.
:rtype: A :class:`Classifier <Classifier>`
"""
```

**注意**
* 使用动词（Return）比名词（Returns）好一些
* 为类的 `__init__` 方法添加文档字符串

```Python
class Person(object):
    """A simple representation of a human being.

    :param name: A string, the person's name.
    :param age: An int, the person's age.
    """
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

##### 注释
有节制地使用注释。提高代码的可读性比写一大堆注释好。通常情况下，短小的方法比注释有效。
**不可以**
```Python
# If the sign is a stop sign
if sign.color == 'red' and sign.sides == 8:
    stop()
```

**可以**
```Python
def is_stop_sign(sign):
    return sign.color == 'red' and sign.sides == 8

if is_stop_sign(sign):
    stop()
```
当撰写注释时，记得运用“Strunk & White”。
>注：参考《The Elements of Style》，中文译名《文体的要素》。
>《The Elements of Style》是一本美国英语的写作指南。作者是William Strunk, Jr.和E. B. White。书中包括8个“基本使用规则”，10个“创作的基本原则”，“一些形式的问题”，一个包含49个易误用的单词和表达，57个易拼错的单词的列表。在2011年，时代 (杂志)把The Elements of Style列为从1923年到现在100本最富有影响力的书之一。（摘自维基百科）

#### 代码行长度
无需再强调了。80-100个字符是合适的。使用小括号来延续代码行。
>注：意思是尽量避免使用反斜杠。

```Python
wiki = (
    "The Colt Python is a .357 Magnum caliber revolver formerly manufactured "
    "by Colt's Manufacturing Company of Hartford, Connecticut. It is sometimes "
    'referred to as a "Combat Magnum". It was first introduced in 1955, the '
    "same year as Smith & Wesson's M29 .44 Magnum."
)
```

### 测试
争取100%的测试覆盖率，但不要迷恋覆盖率数字。
>注：不要为了得到一个好看的测试覆盖率而测试，抓住测试的本质——为了保证软件系统按设计正确稳定地运行。覆盖率越高，不一定能保证系统正确性和稳定性越高。

#### 通用测试准则
* 使用长的描述性的名称。测试方法的名称意义明确就可以避免额外文档字符串或注释。
* 测试应该是孤立的。避免依赖于真实的数据库和网络。使用单独的测试数据库或使用mock对象。（注：每个测试方法也应当是孤立的，尽量不能相互依赖。）
* 倾向于使用[factories](https://github.com/rbarrois/factory_boy)来搭建测试上下文（fixtures）。
> 注：fixtures是指 [test fixtures](https://en.wikipedia.org/wiki/Test_fixture#Software)，测试所依赖的数据、条件等一些列特定环境，使得测试结果是可以重复检验的。也被称为测试上下文（test context）。
* 永远不要让未完成的测试通过，否则有遗忘它们的风险。取而代之的是添加一个占位符，例如``assert False, "TODO: finish me"``。

#### 单元测试
* 专注于每一个小功能。
* 执行要快，但慢的测试比没有测试好得多。
* 通常有必要为每个class或model编写对应的测试类。

```Python
import unittest
import factories

class PersonTest(unittest.TestCase):
    def setUp(self):
        self.person = factories.PersonFactory()

    def test_has_age_in_dog_years(self):
        self.assertEqual(self.person.dog_years, self.person.age / 7)
```

#### 功能性测试
功能性测试是高层次的测试，接近于终端用户与你系统的交互过程，通常用于Web和GUI程序。
* 为应用场景编写测试，测试用例和测试方法的名称应该和场景描述一致。
* 在编写测试代码之前，先写明注释。

```Python
import unittest

class TestAUser(unittest.TestCase):

    def test_can_write_a_blog_post(self):
        # Goes to the her dashboard
        ...
        # Clicks "New Post"
        ...
        # Fills out the post form
        ...
        # Clicks "Submit"
        ...
        # Can see the new post
        ...
```
注意测试用例和测试方法是如何做到连起来读就像“测试一个用户可以发表一篇博客”的。
>注：正常的功能代码也应该有这样的流畅性。如果代码写得简洁优雅，读起来像读诗一样，就能体会到**Art of** computer programming了。

## 灵感来源
* [PEP 20 (The Zen of Python)][1]
* [PEP 8 (Style Guide for Python)][2]
* [The Hitchiker's Guide to Python][3]
* [Khan Academy Development Docs][4]
* [Python Best Practice Patterns][5]
* [Pythonic Sensibilities][6]
* [The Pragmatic Programmer][7]
* 其他

[1]:http://www.python.org/dev/peps/pep-0020/
[2]:http://www.python.org/dev/peps/pep-0008/
[3]:http://docs.python-guide.org/en/latest/
[4]:https://sites.google.com/a/khanacademy.org/forge/for-developers
[5]:http://youtu.be/GZNUfkVIHAY
[6]:http://www.nilunder.com/blog/2013/08/03/pythonic-sensibilities/
[7]:http://www.amazon.com/The-Pragmatic-Programmer-Journeyman-Master/dp/020161622X/ref=sr_1_1?ie=UTF8&qid=1381886835&sr=8-1&keywords=pragmatic+programmer

## 译者注
* [原文链接](https://osf.io/28rmf/wiki/home/)
* 在翻译的基础上有少许注解
* 本文是的小、全、精的Python编码指南，但读罢觉得此文还有待改进和补充。感兴趣的同学可以关注本文的后续更新。
