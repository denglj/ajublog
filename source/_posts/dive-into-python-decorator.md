title: 深入浅出Python装饰器
date: 2016-01-14 13:52:34
tags: Python
---
有朋友说看到《Python CookBook》的装饰器部分有些迷糊，究其原因是没有清晰地理解Python装饰器是什么，能干什么。于是有了本文。看此文时，从头到尾跟着思路走，应该会对Python装饰器有更明白深刻的认识。新手不要跳读，新手不要跳读，新手不要跳读。
本文假定你已经明白了Python中什么叫做函数，变量的作用域、解析规则及生存周期。阿驹还是简单阐述一下：
* **作用域**: 变量起作用的范围， 也称之为命名空间。以方法、类、函数、模块、包、Python内置环境(当前Python解释器的Python Path里所包含的)这几个的程序结构为单元，以`大结构`包含`小结构`，`外层`包含`内层`的方式，把变量放置在这样一个个的空间中，一个程序结构单元就对应一个作用域（命名空间）。每个变量，不加global和nonlocal关键字时，只属于声明该变量的这层作用域。
<!--more-->
* **变量解析规则**: 根据上述可知，作用域是可以相互独立，也可以是相互嵌套存在的。而变量的解析，是从自身所在的作用域出发，逐层往上寻找。并不能去查找和自己独立并列的作用域。
* **变量生存周期**：变量随着它所在的程序结构单元（方法、类、函数、模块、包）的调用而生，随着调用结束而消。调用开始前和结束后试图操作该作用域内的变量，都将出错。

**一定要深刻理解以上三点。一定要深刻理解以上三点。一定要深刻理解以上三点。**
在正式讲解装饰器之前解释上面三个概念的原因是装饰器就是根据这三个概念玩的花样。也是《Python之禅》中最后一句话“Namespaces are one honking great idea -- let's do more of those!”的一个具体体现。

## Python函数的特点
* 是对象，与其他对象无异，具有属性，可以作为参数传递、返回等
* 函数里可以再声明函数（嵌套）

## 函数闭包
一个来自于Scheme的概念，被诸多函数式编程语言实现。也许你听说过装饰器是基于闭包实现的，那么闭包的本质是什么呢？我们先不对闭包下定义。（翠花儿，上代码！）
```py
def outer():
    x, y, z = 1, 2, 3
    def inner():
       a, b = 4, 5
       print("inner's vars sum:", a+b)
       print("use var y of outer:", y)
    return inner
f = outer()
f()
# 输出结果
("inner's vars sum:", 9)
('use var y of outer:', 2)
```
仔细看上述代码。在inner中使用变量y时，根据解析规则，因为inner自己的作用域中未找到，故而到上层的outer的作用域中查找，打印的两行结果也是正确的。
**但是，但是，但是**，有一个问题，我们说过变量的生存周期。不是说函数调用结束时，变量就销毁了吗？ `f = outer()` 执行完时按理说x, y, z及内部的inner函数都应当被销毁了呀？！你也许会想，调用结束时把inner返回给了`f`呀，当然能执行。思考一个问题，`f` 是 `inner` 吗？换个问法，这里等号赋值，是把`inner`的引用传递给了`f`吗？
`f` 不是 `inner` ，因为inner只包含其def声明开始，函数体结束为止的内容，其作用域中只有a，b两个变量，在我们调用f时，却正确打印出了outer作用域中的变量。
似乎，`f` 是 `inner` 加上 `y` 的合体。对了，这就引出了闭包的概念，`f`是一个闭包。下面来给闭包概念做个确切的表述。
> 嵌套定义在非全局作用域中的函数，当它的外部函数被调用就会生成一个闭包，闭包中包含了这个子函数本身的代码与其依赖的外部变量的引用。

注：Python的一个py文件就是一个模块，就是一个全局作用域。闭包不是这个子函数， 而是这个子程序与其依赖的外部变量构成的整体。这个整体构成了一个新的封闭的作用域，所以叫闭包。**上例中，inner子程序并没依赖外部的x和z，所以这个闭包中不包含x,z。**也可以把闭包当做一个新的函数来对待，不过这个新函数的逻辑代码还是原来的子函数代码，但其作用域，却包含了原子函数的变量和其依赖的外部变量。

**另外，每次调用外部函数，其内部函数都会被重新定义，就会生成一个新的闭包。** 这是同一个装饰器可以作用于不同的函数的基础。如下例，其本质不是分别传递参数1,2给inner，而是生成了两个能打印各自数字的闭包。
```py
def outer(x):
    def inner():
        print x
    return inner
print1 = outer(1)
print2 = outer(2)
print1()
# 输出
1
print2()
# 输出
2
```

## “纯手工”装饰器
在上例中，我们给`outer`函数传递的参数是一个整数，然后对这个整数进行了处理（某种装饰和加强，虽然这里只是print了一下）。如果我们传递的是一个函数呢？那它就成了装饰器！我们可以在inner内部执行额外的操作，再返回一个闭包。而这个返回的闭包，就是原函数被装饰后的版本（代替加强版）。**装饰器的本质就是函数闭包，或者说利用了函数闭包的特性。**
```py
def outer(function):
    def inner():
        print("执行function之前可以进行额外操作")
        result = function()
        print("执行function之后还可以进行额外操作")
        result *= 2        # 对function的返回值本身进行额外操作
        return result      # 返回‘加强’后的结果
    return inner

def wait_for_deco():
    return 1024

decorated = outer(wait_for_deco)
print decorated()
# 输出
2048
```
上例就是纯手工实现的一个最简单的装饰器。装饰器函数`outer`并没有修改被装饰函数`wait_for_deco`，但我们调用被装饰后的`decorated`函数闭包却能够得到原函数的加强版结果，还能进行额外的操作。
为了让返回的闭包函数看起来就像是原函数的加强版，我们只需要像下面这么做。
```py
wait_for_deco = outer(wait_for_deco)
```
为了简化代码，Python为我们提供了装饰符`@`，只需要在`wait_for_deco`上面加上`@outer`就可以了。**实际上装饰符`@`就仅仅是帮我们自动地把返回的闭包函数名字替换为原函数的名字。** 使返回后的新函数(闭包)看起来就是原函数，不过是加强了的。
```py
# outer 函数不变

# 使用装饰符简化代码
@outer
def wait_for_deco():
    return 1024

print wait_for_deco()
# 输出
2048
```
以上，装饰器的来龙去脉就讲清楚了。但是它太简单了，被装饰的函数没有参数，装饰器也没有参数。接下来就是对装饰器进行扩展。比如写出能接受任何函数的装饰器，以及装饰器本身可以带参数，以及用类作装饰器，还有装饰器的实际应用。

## 支持带参数的函数
刚才我们提到，装饰器最终返回的是一个闭包，而这个闭包可以看做一个函数，它是原函数的加强版。即是，调用原函数，变成了调用这个被装饰后的闭包。那么原函数的参数如果能够按原样传递给这个闭包函数的话，那么在装饰器中我们应该在其内部函数的定义中按原函数的格式写上参数。这样调用这个闭包时就可以按原来的样子传递参数了。
```py
# 例如为下面的函数写一个装饰器，应该在内部的wapper中按原样传递参数
def decorator(func):
    def wrapper(x, y)
      ret = func(x, y)    # 原函数的返回值
      return ret*2        # 原函数的结果“加强”后再返回
    return wrapper

@decorator
def wait_for_deco(x, y):
  return x + y

print(wait_for_deco(1, 2))

# 输出
6
```
按照上面这种写法虽然可以传参了但有个缺陷，参数个数不确定的函数就没法使用这个装饰器了。比如，原函数有x, y, z三个参数，也想有把结果放大两倍的装饰器呢？能写出通用装饰器吗？能。
```py
def decorator(func):
    def wrapper(*args, **kwargs)
      ret = func(*args, **kwargs)
      return ret*2
    return wrapper

@decorator
def wait_for_deco_a(x, y):
  return x + y

@decorator
def wait_for_deco_b(x, y, z):
  return x + y + z

print(wait_for_deco_a(1, 2))
6
print(wait_for_deco_b(1, 2, 3))
12
```
现在我们可以让装饰器装饰任何形式传参的函数了。而以上两个被装饰的原函数也可以根据任意参数的匹配来简化为一个函数，不属于本文探讨的内容了。`*args, **kwargs` 的具体使用方法和原理，这是Python基础内容，不明白的可以看《Python学习手册》作用域和参数那一章。

## 装饰器带参数
前文中讲解了，装饰符`@`只是帮我们把返回的闭包名字替换为了和原函数一样的名字。像下面这种操作：
```py
after_decorated = decorator(origin_func)
'@'符号做就是
origin_func = decorator(origin_func) # 得到的是已装饰后的闭包函数
```
我们只需要记住一点，最终装饰器需要返回一个**可调用的**对象（闭包函数），我们才能把原函数作为第一个参数传进去。而要装饰器支持参数，类似于下面这样：
```py
@decorator(args)
def wait_for_deco(x, y):
    return x + y
```
按照我们上面讲的`@`的作用，解释器会把上面这个带参数的装饰器像下面这样执行：
```py
wait_for_deco = decorator(args)(x, y)
```
聪明的你已经想到了，`decrator(args)`返回的是最终需要的装饰器就好了。所以，带参数的装饰器就需要写成下面这样：
```py
def decorator(name):
    print("在这里使用装饰器的name参数：", name)
    def wrapper(func):
        print("在这里也可用装饰器的name参数：", name)
        def _wrapper(*args, **kwargs):
            print("这里还可使用装饰器的name参数：", name)
            ret = func(*args, **kwargs)    # 这里进行原函数的计算
            return ret*2
        return _wrapper                    # 返回可调用对象，_wrapper可以接受原函数的参数
    return wrapper                         # 返回真正的装饰器，接受原函数作为第一个参数

@decorator('haha')
def wait_for_deco(x, y):
    return x + y

```

## 用类(class)作装饰器

## 同时使用多个装饰器

## 装饰器实例
