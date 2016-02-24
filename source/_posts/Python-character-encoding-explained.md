title: 字符编码及Python中文处理精解
date: 2015-11-10 18:33:09
tags:
  - Python
  - 字符编码
---
## 本文目的
为 Python 程序员简练地介绍字符编码相关支持，彻底解疑“ Python 中文乱码”，“ Python 2与 Python 3 字符编码差异”等相关问题。使用其他语言的程序员可作参考，道理都是相通的，不过具体处理方式不同罢了。

## 痛苦的根源
  * **起因**
  计算机不能直接识别**字符**（文本的最小组成单位）。
    + 字形：
      字的形状，又叫字图。文字的抽象形状，“小短横线”就是汉字“一”的字形。
    + 字符：
      抽象符号和数字成对编码用于在计算机系统中表示的信息单位。虽然字符本身是抽象的，但它一旦存在于计算机系统中，它就对应了某种特定字符编码方式。字符“一”在GBK编码中是0xD2BB。
    + 字体：
      字的形体。草书、行书、楷书，都叫字体。计算机中的字体如：华文行楷，微软雅黑。

  * **解决办法**
  但是计算机可以识别二进制数，于是采用一个二进制数来指代一个**字符**。
<!--more-->
  * **结果**
  很多人都想到了这个解决办法。比如拉丁字母，他们想到了用十进制65来表示字符'A'。不同国家或者不同组织，他们因为事先没有商量，都用了同一个整数来表示他们自己国家里的某个文字。在不相互沟通的时候相安无事，但相互沟通时，就出现了问题。

## 编码与解码
  先记住，任何信息，存放在存储介质中时，都是二进制流（比特流）。
  * **编码过程**： 字符转换成二进制流表示的过程。
  * **解码过程**： 二进制流转换成字符的过程。
  * **编码规则**： 编码和解码过程中遵循的规则，例如GBK编码，UTF-8编码。

## 字符编码的来龙去脉
### ASCII
  美国信息交换标准代码，最早的通用编码方案。开始时，只用7个比特位就表示完了所有拉丁文字母和一些符号，共128个。后来发现不够用，又启用了第8位，刚好一个字节的长度，共256个字符。

  但是，不同的公司/组织把这扩展出来的128个**码位**指派给了不同的字符，文档交流就困难了。于是ANSI这个组织站出来了，制定了ANSI标准。

  而且人们也发现，ASCII这种单字节（因为它占8个比特位）编码满足不了更多的字符需求，那必须得用多个字节编码。多字节字符集（MBCS）概念就诞生了。

### ANSI
  美国国家标准协会认可的标准。注意，它是一种标准，而不是某种具体编码，可以看做是编码的一种分类。ANSI的标准就是，ASCII码占用的码位及其所指代的字符不许改变，剩下的自己扩展。

  中国人在这个规定上有自己的扩展（如GBK），英国人也有自己的扩展（如ISO-8859-1,即latin-1）。所以ISO-8859-1和GBK都可以称之为ANSI编码，因为它们符合ANSI规定。以Windows系统为例，中文系统中所指的ANSI编码就是GBK，英文系统中的ANSI编码就是ISO-8859-1。

### MBCS
  多字节字符集(Multi-Byte Character Set)，采用不定长度可以是一个字节，也可以是两个字节，也可以是三个字节来进行编码。大多数情况下2个字节就够用了，汉字就分配两个字节，称之为**DBCS**（Double-Byte Chactacter Set）。

  在Linux系统中看得到MBCS说法，在Windows中呢？其实就是ANSI，ANSI只规定了第一个字节的位置是ASCII，超出这个范围的，肯定也是多字节的了。

### CodePage
  代码页，把一种字符编码方式（和字符集有区别，稍后讲解）放在一个CodePage上，编码解码就像翻书查字典似的。很多个代码页，也就容纳了很多种编码方式。
  这个概念最早来自IBM，但也被微软等公司采用。同一种编码方式，在不同的公司制定的CodePage里“页码”也不相同。比如UTF-8，在微软的CodePage里是65001，在IBM里是1208。

  **注意**：MBCS、DBCS、CodePage、ANSI，它们各自指代的不是某种具体编码，而是符合某种规则的编码方式的统称。在不同的操作系统或平台下，它们有一个默认值而已。

  PS： 微软的CP936不等于GBK，它们有几十个不太常用的字符不同。所以绝大多数情况下感觉不到差异。

### Unicode/UCS

  通过以上的介绍知道，各种解决方案都是各自为政，解决不了 **“同一个系统中同时显示全宇宙的所有字符”** 这个问题。

  于是就有两个组织，他们开始着手做这件事情，UCS和Unicode诞生了。

  * **通用字符集**（UCS，Universal Character Set）是由国际标准化组织（ISO）制定的 ISO 10646标准所定义的字符集。通常也译为**通用多八位编码字符集**。
  * **统一码**（Unicode）是由统一码联盟指定的。


  后来发现，一山不容二虎，世界人民不需要两个目的相同但是具体实现却有差异的编码方案。UCS和Unicode握手言和，从 Unicode 2.0 起，采用了和ISO 10646-1的编码方案，它们在相同的码位上都对应同样的字符。尽管这两个组织目前还在相互独立的在发布字符编码标准。

  可能是Unicode名字好记，所以采用更为广泛。关于UCS-2，UCS-4这些概念不再赘述，自行查阅。

#### Unicode，UTF-8，UTF-16它们是什么关系

  UTF-8（Unicode Transformation Format）即Unicode转换格式，8的意思是使用8比特为单位来进行编码。码位小于128时，就是对应的字节值；大于等于128时，就会转换成2、3、4字节的序列。每个字节的序列值介于128~255。

  GBK，GB2312，Latin-1，Big-5，ASCII等，它们的字符集和具体编码实现方式绑定（如GBK字符集就采用GBK编码方式），即字符和存储在介质上的二进制流一一对应。缺陷很明显，字符集扩展性差。Unicode考虑了这个问题，所以它的编码与编码的实现方式没有绑定。而是有多种实现方式，如UTF-8，UTF-16，UTF-32。

  例如字符‘A’在Unicode中的编码是65，但存储在介质上时，二进制流的十六进制表示采用UTF-8时是0x41，而UTF-16**大端模式**是0x00 0x41。

  至于什么是大端模式、小端模式，UTF-X，GBXXX的具体编码实现方式请自行查阅。

### 内码与外码
  * **内码**：存储在介质上时使用的编码形式。例如GBK，GB2312。
  * **外码**：外部输入系统时的编码。例如拼音输入法编码，仓颉输入法编码。


  例如：采用仓颉输入法对汉字“驹”的编码是`NMPR`，输入系统以后得到字符“驹”。使用GBK作为内码进行存储时就是`0xBED4`。

## 乱码产生的原因
  **编码和解码时用了不同或者不兼容的字符编码方式。**就算同是Unicode，UTF-8和UTF-16也是不同的。

  **解决乱码问题，需要把握的要点**：
  * **输入某软件系统时字符所采用的编码是什么？**（从数据库或文件读取时，原来存储时的编码是什么？从网页抓取时，网页的编码是什么？从控制台输入时，控制台的编码方式是什么？）
  * **软件系统中的编码方式是什么？**（原本若是UTF-8存储，GBK编码的软件系统该如何处理？）
  * **输出时的编码方式是什么？**（如Python脚本处理后的字符串是Unicode编码，输出到采用GBK编码的Windows控制台时应该做什么？）

## Python 2.x字符编码问题
### Python 2 字符（串）类型探究
  * **`basestring`** ：str 和 unicode 对象的基类，抽象的，不可被调用或实例化，仅可用于类型检查。`isinstance(obj, basestring)` 等价于 `isinstance(obj, (str, unicode))`。

  ```python
    >>> isinstance('驹', basestring)
    True
    >>> isinstance(u'驹', basestring)
    True
  ```

  * **`str`** ：实际是字节串。Python2中也有`bytes`对象。`bytes == str` 结果为`True`。例如：‘驹’，这个字符串长度为1，但`len('驹') `在windows平台下是2（默认GBK），Linux平台下是3（默认UTF-8）。

  ```Python
    >>> isinstance('驹', str)
    True
    >>> isinstance(u'驹', str)
    False
  ```

  * **`unicode`** ：`unicode(string[, encoding, errors])` ,按照encoding参数指定的编码方式把参数string转换成unicode。unicode编码的字符串对象，也可以直接加前缀u表示。`len(u'驹')`在Windows和Linux下都是1。

  ```Python
    >>> isinstance('驹', unicode)
    False
    >>> isinstance(u'驹', unicode)
    True
  ```

  unicode构造器的参数encoding默认是7位ASCII编码，所以默认时传入的字节串或字符串的每一个字节值必须小于128：

    * Windows系统下：
    ```Python
      # Win平台默认情况下就是GBK编码，所以以下结果就是GBK编码值
      >>> '驹'
      '\xbe\xd4'

      # 报错，试图用 unicode 方法默认的 ASCII 编码去解码 GBK 编码的字符
      >>> unicode('驹')
      Traceback (most recent call last):
      ......
      UnicodeDecodeError: 'ascii' codec can't decode byte 0xbe ...

      #正确，录入时以GBK编码，故而以GBK方式去解码，再转换成 Unicode 形式
      >>> unicode('驹', 'GBK')   # 等价于 u'驹'
      u'\u9a79'
    ```

    * Linux系统下：
    ```Python
      # Linux平台默认情况下就是UTF-8编码，所以以下结果就是UTF-8编码值
      >>> '驹'
      '\xe9\xa9\xb9'

      # 报错，试图用 unicode 方法默认的 ASCII 编码去解码 UTF-8 编码的字符
      >>> unicode('驹')
      Traceback (most recent call last):
      ......
      UnicodeDecodeError: 'ascii' codec can't decode byte 0xbe ...

      #正确，录入时以GBK编码，故而以GBK方式去解码，再转换成 Unicode 形式
      >>> unicode('驹', 'UTF-8')   # 等价于 u'驹'
      u'\u9a79'
    ```

  > **总结1： 通过系统shell录入或直接在程序中定义的字符串，Python 2 中默认就是str对象（字节串），其字节值是由系统默认编码方式编码所得，Windows是GBK编码方式，Linux是UTF-8。**
  >
  > 所以我们向某个媒介（文件、网页/浏览器、系统Shell或其他软件呈现文本的区域）输出字符串内容时，要用该媒介接收的编码形式编码字符串后再传递给它。

  例如，要从shell中输出字符串时（新手常用urlopen打开一个网页，然后print网页内容），必须将字符串转换为当前的shell使用的编码形式。如，Windows 的 cmd 是GBK，Linux 的 bash 是UTF-8。PyCharm 自带的shell不管是Windows还是Linux都是UTF-8。

### encode 与 decode 方法
* `S.encode([encoding[,errors]]) -> object`

  文档解释：用编解码器注册的编码方式来**编码**字符串S。encoding参数的默认值就是Python默认的编码方式。errors默认值是'strict'，严格模式，一旦编码出错就抛出 UnicodeEncodeErrors 异常，还可以是'ignore'（忽略错误）, 'replace'（将出错的字串替换，一般是替换为问号）。

* `S.decode([encoding[,errors]]) -> object`

  文档解释：用编解码器注册的编码方式来**解码**字符串S。参数与encode的一致。

* Linux平台下实验观察（Win平台结果也同样，不过是win默认为GBK编码）

  ```Python
    >>> s1 = 'abc驹'        # Python2下默认的是str对象，实际是字节串，utf-8编码的
    >>> s2 = u'abc驹'       # unicode字符串
    >>> s1
    'abc\xe9\xa9\xb9'
    >>> s2
    u'abc\u9a79'

    # 把 str 对象，已经是utf-8编码形式的字串再来encode
    >>> s1.encode('UTF-8')
    Traceback (most recent call last):
    # 奇怪的是此处报 UnicodeDecodeError 注意我们用的是encode方法
    UnicodeDecodeError: 'ascii' codec can't decode byte 0xe9 ...

    # 把unicode字串按utf-8形式编码，正确
    >>> s2.encode('UTF-8')
    'abc\xe9\xa9\xb9'

    # 把unicode字串按gbk形式编码，正确
    >>> s2.encode('GBK')
    'abc\xbe\xd4'

    # 把unicode字串按ASCII编码，用法正确，但报错，因为ASCII编码不了'驹'这个汉字。
    >>> s2.encode('ASCII')
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    UnicodeEncodeError: 'ascii' codec can't encode character u'\u9a79' ...

    # 把unicode字串按ASCII编码，忽略编码不了的错误，不会报错，只剩下能被ASCII编码的字符
    >>> s2.encode('ASCII', 'ignore')
    'abc'
  ```

 >**总结2： encode的作用是将unicode对象（没有某种具体编码）字串按encoding参数给定的编码方式转换为str对象（具有某种具体编码）字串。故而用str对象调用encode方法是错误的。**

 >**总结3： decode的作用是将str对象字串（某种具体编码的）按照encoding参数给定的编码方式解码成unicode对象。故而用unicode对象调用decode方法是错误的。**

  所以，在使用encode方法时，首先确认是unicode对象在调用它，这个方法会将unicode形式转换成某种具体编码形式的字符串。decode方法是将某种具体的编码的字串解码还原成unicode统一码，必须确认是str对象在调用它，而且encoding参数必须是调用它的字符串的具体编码形式，否则可能出错。

  上面三段话多读几遍，被我洗一下脑，然后就明白什么时候该encode, 什么时候该decode，什么时候该传gbk，什么时候该传utf-8，而不是乱猜的。

## Python 3.x字符编码问题
  看完了Python2.x中，什么basestring, str, unicode, 一会儿Python默认ascii编码，一会儿系统默认是GBK编码，也是醉了，所以处理多字节编码时一不小心就会出错。

  Python3.x 字符串编码问题轻松了许多，不存在unicode对象了。不论是 `'驹'` 还是 `u'驹'` 这两种形式，得到的对象都是`str` 对象，但内部是unicode方式编码，所以Python3的`str`对象等价于Python2的`unicode`对象。

  那么Python2中的`str`（字节串）对象在Python3中跑到哪儿去了？ 就是`bytes`对象，就是个字节序列。

  而且Python3中更严格。我们知道Python2中不论是str还是unicode对象，都可以调用decode或encode方法，太混乱。

  在Python3中的`str`（Python2中的`unicode`），它是统一码，没有某种具体形式，所以只能被某种具体形式编码，只能调用encode，而不能调用decode。同样，Python3中的`bytes`对象,如b'\xe9\xa9\xb9'(UTF-8编码形式的'驹'字)，已经具备某种具体编码，只能被解码还原成unicode统一码，所以它之只能调用decode方法，调用encode会报错。

 >总结4：
 >Python2 ： `str`--(decode)--> `unicode` --(encode)--> `str`
 >Python3 ： `bytes` --(decode)--> `str` --(encode)--> `bytes`

  Python2中的 str 实际是字节串 bytes , Python3中的 str 实际是unicode编码。不论是Python2或Python3，编码转换都须以unicode作为桥梁。那么decode，encode连用的形式就是用于编码转换。

  **对于新手来说重要的例子**，在Windows的cmd中写了几句脚本，抓取了一个UTF-8编码的网页，要在cmd里正确打印，就需要`page_content.decode('UTF-8').encode('GBK')`，这样打印就不会出乱码。而在linux平台下，因为bash默认是UTF-8方式编码，所以直接`print page_content`就可正确显示中文字符。

## 终极案例
  写了一个爬虫，抓取的是BIG5编码的繁体中文页面，每抓取一条记录需要从windows控制台打印出来供开发人员查看，而且开发人员还要手动输入一个人工检测编号。然后将检测编号与抓取内容存入以UTF-8方式存储数据的MySQL。虽然是windows平台，但要求以UTF-8的编码保存日志文件。另外其中某段内容你要引用下来放在自己GB2312编码的网页上。

  1. 如果涉及的编码方式较多，管它三七二十一，先将具体编码的转换成unicode的
    `uni_content = page_content.decode('big5')`
  2. 打印至windows控制台
    `print uni_content.encdoe('gbk')`
  3. 开发人员从cmd输入编号
    `no = input('请输入编号')   # python2用 raw_input() ,得到的是gbk编码形式的字串`
  4. 将编号与原内容组合，存入MySQL，日志也是UTF-8，类似的
    `new_content` = (no.decode('gbk')+uni_content).encode('utf-8')
  5. 将以是UTF-8编码的内容从数据库里拿出来，放入GB2312网页上
    `new_page = sql_content.decode('UTF-8').encdoe('GB2312')`

## 再次提示
要解决乱码，明白因为编码解码的方式不一致导致的本质，知道每一次操作中输入是什么编码形式，输出需要什么编码形式，通过unicode为桥梁，去解决问题就好了。

要注意的是，编码间转换数据丢失的问题。例如`'abc驹'` 能够转换为unicode编码, 通过unicode转为ascii也是可以的，但ascii无法编码汉字，erros参数设为'ignore'时不报错，但只保留下了`'abc'`。

## 更多参考
* [Python3 如何使用 unicode](https://docs.python.org/3.5/howto/unicode.html)
* [Python2 如何使用 unicode](https://docs.python.org/2/howto/unicode.html)
