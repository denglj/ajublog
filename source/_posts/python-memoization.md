title: Python性能优化之记忆化（Memoization）
date: 2016-01-22 14:43:16
tags:
- Python
- 性能优化
---
## 记忆化（Memoization）介绍
简单说来 Memoization 是一种利用缓存来加速函数调用的技术手段，将消耗较大的调用结果存储起来，当再次遇到相同调用时就从缓存读取结果而无需重新计算。
**有一个限制条件是该函数必须是纯函数式的**，相当于函数式编程中的不可变性，即输入一致时输出一定不会改变。比如计算平方的函数就满足这种条件，`square(3)` 的结果永远是`9`，所以我们才能把它的结果存储起来，下次需要知道`3`的平方的结果时，无需计算，直接从内存中读取就好了。
<!--more-->
而那些依赖于可变的参数的函数，则不可使用这种方式进行加速。比如获取系统当前登录用户数量的函数，也许参数都是固定的，但返回值却是可变的，这是由系统中即时登录的用户数量决定的，缓存起来就没意义了。

这里的缓存介质，可以是内存，可以是硬盘上的文件，可以是数据库，总之，能使之加速则认为是有效的。我们经常提及的 `memcached` 分布式内存对象缓存系统也是一种基于记忆化（memoization）的优化方式。今天不讲 `memcached` ，而是一种更简便小巧的办法，然而，它们的基本原理是一样的，它们都实现了LRU（Least Recently Used）缓存算法， 只缓存最少的最近使用过的数据。

## Python内置函数缓存装饰器
**`@functools.lru_cache(maxsize=128, typed=False)`**，`maxsize` 参数是指最大缓存多少个调用，如果赋值为 `None` 则是无限制缓存，且关闭 LRU 功能。`typed` 参数控制函数参数类型不同时是否单独缓存。设置为`True`时， 例如`f(3)`和`f(3.0)`将会区别对待。

下面这个例子是缓存静态web内容的：

```py
from functools import lru_cache

@lru_cache(maxsize=32)
def get_pep(num):
    'Retrieve text of a Python Enhancement Proposal'
    resource = 'http://www.python.org/dev/peps/pep-%04d/' % num
    try:
        with urllib.request.urlopen(resource) as s:
            return s.read()
    except urllib.error.HTTPError:
        return 'Not Found'

>>> for n in 8, 290, 308, 320, 8, 218, 320, 279, 289, 320, 9991:
...     pep = get_pep(n)
...     print(n, len(pep))

>>> get_pep.cache_info()
CacheInfo(hits=3, misses=8, maxsize=32, currsize=8)
```
`lru_cache`装饰器是Python3.2新增的，Python3.3新增了`typed`可选参数。

## Python2的实现
Python2中没有`lru_cache`装饰器，但是我们可以自己实现，非常简单，如下：
```py

_CACHE_VALUES = {}

def memoized(func):
    def wrapper(x):
        return _CACHE_VALUES.get(x, func(x))
    return wrapper

@memoized
def addtwo(x):
  return x+2
```
但我们上面这种写法太简单了，连缓存命中率这些统计都没有，而且也不通用。但明白了基本原理，我们要扩展也就不难了。更加复杂的装饰器实现可以参考[《深入浅出Python装饰器》](http://ajucs.com/2016/01/14/dive-into-python-decorator.html)。
更偷懒一点，你可以把Python源码中的lru_cahce函数相关代码拷贝到项目中，[参考这里](https://github.com/python/cpython/blob/77a475ebaa65aa5a85287cbe782c552039c9f507/Lib/functools.py#L391)。
