title: 如何在Python里应用SOLID原则
date: 2016-06-17 12:10:31
tags:
 - Python
 - OOP
---
如今OOP编程大行其道。不少人以为用编程语言里的class关键字定义一个类，然后用类创建一个对象就是OOP了。肤浅！

OOP编程很贴近人们的正常思维方式，所以容易被接受，而且应用也很广泛。的确，这给编程带来了很大的好处。但并不是任何人都能深谙OOP的本质。简略证明如下：如果把“女朋友”作为一个类，你自己的女朋友就是你的对象，是“女朋友”类的具体的实例。如果你能熟练掌握“女朋友”这个类的特性以及方法，还不能创建一个实例么？你没女朋友，说明你没吃透女朋友类，进而说明你肯定没理解好OOP。但创建了一个实例出来也并不能证明你就吃透了这个类。
<!--more-->

以上是闲话。

是否感觉你在OOP时总是要么很随意的定义class然后创建object，要么在生搬硬套所谓的设计模式？

是否感觉你的程序真的很脆弱（比如一天的告警邮件就是几千封）？

是否觉得灵活性差可扩展性不足（比如要增加新功能，不能通过某种优雅的方式插入现在的系统，而是copy一份类似的代码改一改）？

是否觉得子模块/子系统之间依赖关系混乱，粘黏性强（你渴望用TDD或unittest来保证系统每次迭代的质量，但你发现基于你的代码难以写testcase，为啥？因为依赖混乱、粘黏性强 独立的程序单元基本没有，没有unit请问怎么unittest？）？

如果我们的代码存在以上任何一种问题，包括没提到的一些导致系统烂的问题，如果你还在用OOP的话，那么好好根据本文思考一下你的代码该如何改进。

## SOLID是什么？
**SOLID（单一功能、开闭原则、里氏替换、接口隔离以及依赖反转）**是由罗伯特·C·马丁（其著作有《敏捷软件开发——原则、模式与实践》、《Clean Code》）在21世纪早期引入的记忆术首字母缩略字，**指代了面向对象编程和面向对象设计的五个基本原则。**

SOLID被典型的应用于测试驱动开发（TDD，TDD也并不那么美好，以后再说），并且是敏捷开发以及自适应软件开发的基本原则的重要组成部分。

如果你们在践行敏捷开发和尝试TDD，那么有什么理由不掌握这五个几本原则呢？这五个原则并完全是罗伯特·C·马丁原创的，别弄混了。

**但是，原则并不是规则，更不是教条，原则对智者来说是指导，对愚者来说是遵从。** SOLID以及本文只起到抛砖引玉的作用。

| 首字母  | 指代 | 概念    |
|:------:|---|-----------|
| S  |单一功能原则 |对象应该仅具有一种单一功能|
| O  |开闭原则     |软件体应该是对于扩展开放的，但是对于修改封闭的 |
| L  |里氏替换原则 |程序中的对象应该是可以在不改变程序正确性的前提下被它的子类对象所替换的|
| I  |接口隔离原则 |多个特定客户端接口要好于一个宽泛用途的接口|
| D  |依赖反转原则 |高层次的模块不应该依赖于低层次的模块，两者都应该依赖于抽象接口；抽象接口不应该依赖于具体实现，而具体实现则应该依赖于抽象接口。|

## 为什么要用SOLID原则指导OOP？
**容易编写易于维护的、复用率高的、易于测试的OO代码。** 为了达到这个目的，你可以自己写上数十万行代码并研究总结出类似的规律，也可以先在这个原则的指导下试试看。

## Talk is cheap. Show me the code.
现在我们要用OOP来实现一段最基本的 “洗车服务” 代码。需求如下：
```
洗车服务
  - 洗车作业任务
      汽车进入洗车机时
        注册洗车任务
  - 顾客通知
      洗车完毕
        向顾客发出消息通知
  - 报表
      客户端发出报表请求时
        向该顾客展示他的所有洗车信息
```

也许你的代码其中一段如下：
```python
class CarWashService(object):

    def __init__(self, sms_sender):
        self.persistence = {}
        self.sms_sender = sms_sender

    def require_car_wash(self, car, customer):
        service_id = uuid.uuid4().hex
        self.persistence[service_id] = (car, customer)
        return service_id

    def wash_completed(self, service_id):
        car, costomer = self.persistence[service_id]
        self.sms_sender.send(mobile_phone=customer.mobile_phone,
            text='Car %{car.plate} washed'.format(car=car))
```

## 单一职责原则
为什么要遵循这个原则？交警在路边可以去劝阻路边打架斗殴的，在民警未到时也应该去劝阻，但交警就该去劝架了吗？道路交通怎么办？让民警又干什么？在纠纷频发的地方如果只有交警而无民警，那是治安体制有问题没在那里安插民警，而不是交警袖手旁观。

所以，**分清楚你“必须做”和“可以做”的事情**。每个角色做好必须做的事情就很好了。如果还有一些事情没人做，那就创造角色让他去做。

那么以单一职责原则来看上面的代码有什么问题？
```python
class CarWashService(object):

    def __init__(self, sms_sender):
        self.persistence = {}    # A
        self.sms_sender = sms_sender  # B

    def require_car_wash(self, car, customer):
        service_id = uuid.uuid4().hex
        self.persistence[service_id] = (car, customer)    # A
        return service_id

    def wash_completed(self, service_id):
        car, costomer = self.persistence[service_id]      # A
        self.sms_sender.send(mobile_phone=customer.mobile_phone,
            text='Car %{car.plate} washed'.format(car=car))    # B
```

经过一点改造后，写出了下面的代码：
```python
class CarWashService(object):

    def __init__(self, notifier, repository):
        self.notifier = notifier
        self.repository = repository

    def enter_in_car_wash(self, car, customer):
        job = CarWashJob(car, costomer)
        self.repository.put(job)
        return job

    def wash_completed(self, service_id):
        car_wash_job = self.repository.find_by_id(service_id)
        self.notifier.job_completed(car_wash_job)

    def service_by_customer(self, customer):
        return self.repository.find_by_customer(customer)
```

## 依赖反转原则
为何要遵循这个原则？你会把台灯电线直接焊接在墙上的电线上吗？

**高层次的模块不应该依赖于低层次的模块，两者都应该依赖于抽象接口。**台灯是高层次模块，电路是低层次模块，台灯依赖于台灯的插头而非电线以获取电源；电路依赖于插座为外部提供电源而非直接将电线暴露出去。

**抽象接口不应该依赖于具体实现，而具体实现应该依赖于抽象接口**。三针插头可以被台灯用，也可以被冰箱用，所以抽象接口（插头）并不依赖于背后的具体实现（台灯/冰箱）。而冰箱因为功率较大，一定要有能接地线的三针插头，所以具体实现依赖于抽象接口。

在Python中，编译或程序启动时的依赖是import语句的内容，而运行时的依赖就是被调用的函数、方法等。

也许你写出了类似下面的代码：
```python
class CarWashService(object):

    def __init__(self, repository):
        self.repository = repository
        # self.notifier = SmsNotifier()

    def enter_in_car_wash(self, car, customer):
        job = CarWashJob(car, customer)
        self.repository.put(job)
        return job

    def wash_completed(self, service_id):
        car_wash_job = self.repository.find_by_id(service_id)
        SmsNotifier.send_sms(car_wash_job)    #有问题
        # self.notifier.send_sms(car_wash_job)
```

这段代码的问题主要出在直接调用`SmsNotifier`类方法那行。如果你是按被注释的那两行写的，还是存在如下同样的问题。

**问题一是全局状态问题**，直接使用了全局变量`SmsNotifier`，我们说过写代码能使用局部变量的就别使用全局变量，一是全局变量不使用时并不会被释放内存，二是全局变量对其他对象来说都是可见的，也是可修改的，会给程序带来更多的不确定性。

**问题二在于隐式依赖问题**。并不能从`CarWashService`的构造方法或初始化方法中知道它依赖了`SmsNotifier`类，这对代码的可读性和易于理解性都会带来障碍。比如在别的模块中使用了`CarWashService`类的时候，不层层追查的话，神仙才知道它还依赖了`SmsNotifier`。

**问题三是依赖于具体的实现**。`CarWashService`对象直接依赖了具体的`send_sms`方法。这样做能够实现通知的需求，但是**扩展性差**。如果又要同时用电话、邮件、微信等通知方式，又该要来改这里的老代码了。从现实世界中的例子来看，在较大的机构中都会有类似“传达室”的小机构。要怎么改进就显而易见了。

改进后的代码如下：
```python
class CarWashService(object):

    def __init__(self, notifier, repository):
        self.repository = repository
        self.notifier = notifier

    def enter_in_car_wash(self, car, customer):
        job = CarWashJob(car, customer)
        self.repository.put(job)
        return job

    def wash_completed(self, service_id):
        car_wash_job = self.repository.find_by_id(service_id)
        self.notifier.job_completed(car_wash_job)
```

上面通过**依赖注入**的方式将`notifier`对象作为初始化参数传递给`CarWashService`类。这种写法化解了上面提到的三种负面影响。

**依赖注入是实现依赖反转的一种方式，两者并不等同。**两个存在依赖关系的对象A、B，A使用B的服务，B可以向A提供服务，我们并不让A直接使用B，而是将B传递给A，使B成为A的一部分。这就是依赖注入。

其实**依赖反转**也相当于应用了**适配器模式**，举例中的插座和插头是抽象接口，也就是台灯电线和供电电线的适配器，传达室也是消息发送人和消息接收人的适配器。示例代码中被抽象出来的notifier就是适配器对象。洗车服务只知道洗完车要通知，但是具体用哪些途径通知，当前结束的任务该通知给谁，在什么时间通知，都由`notifier`去完成。程序的耦合性会进一步降低，灵活性进一步增强。

## 开闭原则
为何要遵循开闭原则？当你想增加自己的御寒能力只用在身体外加衣服而非做个开胸手术。软件体也一样，观察人体这个造物者的完美之作，把它的规律用在软件体上，就可以造出更完美的软件。**好的设计可以让你在为系统新增功能时添加新代码即可而无需修改老代码。**

洗车服务的数据需要得到保存，可能保存在内存、文件、数据库等等。但这些功能都是几乎一致的，所以你很可能写出了如下抽象类，希望其他子类都来继承它。
```python
class InterfaceJobReository:

    def put(self, job):
        raise NotImplementedError()

    def find_by_id(self, job_id):
        raise NotImplementedError()

    def find_by_customer(self, customer):
        raise NotImplementedError()


class InMemoryJobReository(InterfaceJobReository):
	"""注意这里的继承"""

    def __init__(self):
        self._storage = {}

    def put(self, job):
        self._storage[job.service_id] = job

    def find_by_id(self, job_id):
        return self._storage.get(job_id)

    def find_by_customer(self, customer):
        return [job for job in self._storage.values()
                if job.has_customer(customer)]
```

以上写法这是符合开闭原则的，因为对于扩展成用文件保存时，只需要另外增加一个`InFileJobReository`类并写出相关实现就好了，并不会动任何一行已有代码。

**秉承鸭子类型的理念，我们还可以简化代码，可以无需写那个抽象类**。只需要让子类都继承Python的`object`类，例如`InMemoryJobReository(object)`， 剩下的一个字符都不用变，也能达到同样效果，但这似乎也留下了更多犯错误的可能，其中平衡点自行拿捏。

## 里氏替换原则
为何要遵循这个原则？古猿作为基类，直立人是古猿的后代，现代人是直立人的后代，现代人可以代替直立人这是很自然的事情。这是自然法则和规律，为什么不可以应用到软件中来？如果你有一天看到个看起来像现代人，叫起来也像现代人，而她却需要充电，那她肯定是基于错误的基类生成出来的。
```python
class InMemoryJobReository(dict):
	"""注意这里的继承"""

    def put(self, job):
        self[job.service_id] = job

    def find_by_id(self, job_id):
        return self.get(job_id)

    def find_by_customer(self, customer):
        return [job for job in self.values()
                if job.has_customer(customer)]
```

原先是直接使用`dict`类的对象赋给`self._storage`，来完成工作。现在继承了`dict`类，相当于原来直接使用父类`dict`，而现在使用的是`dict`的子类。现在`InMemoryJobReository`的对象完全可以代替父类了。

Python在接口实现时并不强制性继承。就算A和B是完全不相关的类，你还可以通过`abc`模块来将A类注册给B类，让A成为B的虚拟子类。如此强大灵活，比起东拼西凑，多多考虑如何提高到代码的重用率。

## 接口隔离原则
为何要这么做？人嘴巴和鼻孔的功能应该不一样吧？否则的话，全人体只需要一个孔就行了（嘿~ 嘿~ 嘿~）。

本节就没有代码了，因为示例只直接地讲了一个接口，不过我们在这过程中拆分了一些接口，不是吗？

很多时候，根据这五条原则多多设计后再写代码，就能写出很好的代码了。这些基本原则在很多情况下也是相互促进，相互兼容，相互满足的。这过程中针对实际情况的不同你自己做的变通，也就相当于是运用了各类设计模式。比如在依赖反转一节中提到的适配器模式，控制反转模式等等。
