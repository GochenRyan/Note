问题： python的原生字符串不能以反斜杠结尾

解决办法：把反斜线放在另一个字符串里，Python会自动进行合并

```
dir = r"\this\is\my\dos\dir" "\\"
```

---

问题：使用相对路径：`No such file or directory`

解决办法：

* Make sure you're in the directory you think you're in with `os.getcwd()` (if you launch your code from an IDE, you may well be in a different directory)

* You can then either:
  
  * Call `os.chdir(dir)`, `dir` being the folder where the file is located, then open the file with just its name like you were doing.
  
  * Specify an absolute path to the file in your `open` call.

--- 

Python `splitlines() `按照行('\r', '\r\n', \n')分隔，返回一个包含各行作为元素的列表，如果参数 keepends 为 False，不包含换行符，如果为 True，则保留换行符。

---

浅拷贝：

`from copy import copy`

标识一个对象唯一身份的是：对象的id。浅拷贝就是创建一个具有相同类型，相同值但不同id的新对象。

对**可变对象**而言，对象的值一样可能包含有对其他对象的引用，浅拷贝产生的新对象，虽然具有完全不同的id，但是其值若包含**可变对象**，这些对象和原始对象中的值包含同样的引用。**可变对象**的值在发生改变时会对原对象的值产生副作用，因为这些值是同一个引用。

深拷贝：

`from copy import deepcopy`

深拷贝不仅仅拷贝了原始对象自身，也对其包含的值进行拷贝，它会递归的查找对象中包含的其他对象的引用，来完成更深层次拷贝。因此，深拷贝产生的副本可以随意修改而不需要担心会引起原始值的改变。

深拷贝并非完完全全递归查找所有对象，因为一旦对象引用了自身，完全递归可能会导致无限循环。一个对象被拷贝了，python 会对该对象做个标记，如果还有其他需要拷贝的对象引用着该对象，它们的拷贝其实指向的是同一份拷贝。

注意事项：

要避免使用deepcopy，直接使用deepcopy看不到数据结构，不利于代码理解，而且效率差。深拷贝需要维护一个 memo 用于记录已经拷贝的对象，这是它比较慢的原因。在绝大多数情况下，程序里都不存在相互引用。但作为通用模块，Python 深拷贝必须为了这 1% 情形，牺牲 99% 情形下的性能。可以创建新的对象，逐一复制原对象中的不可变对象。

---

Python字典formkeys()方法用于创建一个新的字典，并以可迭代对象中的元素分别作为字典中的键，且所有键对应同一个值，默认为None。

---

list.sort(cmp=None, key=None, reverse=False)

* **cmp** -- 可选参数, 如果指定了该参数会使用该参数的方法进行排序。

* **key** -- 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。

* **reverse** -- 排序规则，reverse = True 降序， reverse = False 升序（默认）

```python

list.sort(key=lambda x: len(x))
```

---

**pycharm书签**

Ctrl+Shift+数字：添加标签

Ctrl+数字：跳转

---

**将整数的每一位拆分**

```python
nList = [int(i) for i in list(str(n))]
```

```python
l = []
while (n>0):
    l.append(n%10)
    n=n/10
l=list(reversed(l))
```

---

导表生成嵌套字典格式

```python
myDict = {}
myDict.update({1001: {1: "hello", 2: "world"})
...
```

---

## str.split()

### 描述

split()通过指定分隔符对字符串进行切片，如果参数num 有指定值，则仅分隔 num 个子字符串。

### 语法

```
str.split(str="", num=string.count(str)).
```

### 参数

- str -- 分隔符，默认为所有的空字符，包括空格、换行(\n)、制表符(\t)等。
- num -- 分割次数。

str.split不支持正则及多个切割符号，不感知空格的数量，比如用空格切割.因此split只适合简单的字符分割

## re.split()

支持正则及多个字符切割

```python
>>> print line  abc aa;bb,cc | dd(xx).xxx 12.12'    xxxx    
按空格切  
>>> re.split(r' ',line)  ['abc', 'aa;bb,cc', '|', 'dd(xx).xxx', "12.12'\txxxx"]    
加将空格放可选框内[]内  
>>> re.split(r'[ ]',line)  ['abc', 'aa;bb,cc', '|', 'dd(xx).xxx', "12.12'\txxxx"]    
按所有空白字符来切割：\s（[\t\n\r\f\v]）\S（任意非空白字符[^\t\n\r\f\v]    
>>> re.split(r'[\s]',line)  ['abc', 'aa;bb,cc', '|', 'dd(xx).xxx', "12.12'", 'xxxx']    
多字符匹配  
>>> re.split(r'[;,]',line)  ['abc aa', 'bb', "cc | dd(xx).xxx 12.12'\txxxx"]  >>> re.split(r'[;,\s]',line)  ['abc', 'aa', 'bb', 'cc', '|', 'dd(xx).xxx', "12.12'", 'xxxx']    
使用括号捕获分组的适合，默认保留分割符  
re.split('([;])',line)  ['abc aa', ';', "bb,cc | dd(xx).xxx 12.12'\txxxx"]  #就是说，多个元素之间之前的分隔符保持存在  
去掉分隔符，加?:  
>>> re.split(r'(?:;)',line)  ['abc aa', "bb,cc | dd(xx).xxx 12.12'\txxxx"] 
```

---

**四舍五入**

```python
fA = 2.99999999
fB = "%.2f" % fA
iB = int(round(fA))
```

**直接舍入**

```python
fC = str(fA).split('.')[0] + '.' + str(fA).split('.')[1][:2]
iC = int(fA)
```

---

__metaclass__

---

type

---

\_\_call\_\_

---

**memory_profiler**

作用：memory_profiler是用来分析每行代码的内存使用情况

**使用方法一：**

　　1.在函数前添加 @profile

        2.运行方式： python -m memory_profiler memory_profiler_test.py     

　　此方法缺点：在调试 和 实际项目运行时 要 增删 @profile 此装饰器

Mem usage: 内存占用情况

Increment: 执行该行代码后新增的内存

**使用方法二：**

　　1.先导入：    from memory_profiler import profile

       2.函数前加装饰器：   @profile(precision=4,stream=open('memory_profiler.log','w+'))            

　　　　　　　　　　　　参数含义：precision:精确到小数点后几位 

　　　　　　　　　　　　　　　　　stream:此模块分析结果保存到 'memory_profiler.log' 日志文件。如果没有此参数，分析结果会在控制台输出

　　运行方式：直接跑此脚本  python memory_profiler_test.py  

　　此方法优点：解决第一种方法的缺点，在 不需要 分析时，直接注释掉此行

**使用方法三：**

　　脚本代码和方法二一样，但是 运行方式不同

　　mprof run memory_profiler_test.py       : 分析结果会保存到一个 .dat格式文件中

　　mprof plot                                              : 把结果以图片到方式显示出来（直接在本目录下运行此命令即可，程序会自动找出.dat文件） （要安装  `pip install matplotlib`）

       mprof clean                                           : 清空所有 .dat文件

---

单例

singleton.py

```python
g_lSingletonCls = []

def Reset():
    global g_lSingletonCls
    for oCls in g_lSingletonCls:
        oCls.ClearInstance()

class Singleton(type):
    def __init__(cls, name, bases, dct):
        super(Singleton, cls).__init__(name, bases, dct)
        cls.instance = None

    def __call__(cls, *args, **kwargs):
        print "call"
        if cls.instance is None:
            cls.instance = super(Singleton, cls).__call__(*args, **kwargs)

            global g_lSingletonCls
            g_lSingletonCls.append(cls)

        return cls.instance

    def ClearInstance(cls):
        print "clear"
        cls.instance = None

    def HasInstance(cls):
        return cls.instance != None

class CCase(object):
    __metaclass__ = Singleton  #在代码执行到这里的时候，元类中的__new__方法和__init__方法其实已经被执行了，而不是在CCase实例化的时候执行，且仅会执行一次。
```

test.py

```python
#-*- coding:utf-8
import singleton


oCase = singleton.CCase()
print "before", singleton.CCase.HasInstance()

singleton.CCase.ClearInstance()
print "after", singleton.CCase.HasInstance()
```

---

weakref.ProxyType

不可调用对象的代理的类型对象。

*class* `weakref.``ref`(*object*[, *callback*])[](https://www.osgeo.cn/cpython/library/weakref.html#weakref.ref "永久链接至目标")

返回弱引用 *object* . 如果引用对象仍处于活动状态，则可以通过调用引用对象来检索原始对象；如果引用对象不再处于活动状态，则调用引用对象将导致 [`None`](https://www.osgeo.cn/cpython/library/constants.html#None "None") 将被退回。如果 *回调* 是否提供 [`None`](https://www.osgeo.cn/cpython/library/constants.html#None "None") ，并且返回的weakref对象仍处于活动状态，将在对象即将完成时调用回调；弱引用对象将作为唯一参数传递给回调；引用将不再可用。

允许为同一对象构造许多弱引用。为每个弱引用注册的回调将从最近注册的回调调用到最早注册的回调。

回调引发的异常将在标准错误输出中注明，但不能传播；它们的处理方式与从对象引发的异常完全相同。 [`__del__()`](https://www.osgeo.cn/cpython/reference/datamodel.html#object.__del__ "object.__del__") 方法。

弱引用是 [hashable](https://www.osgeo.cn/cpython/glossary.html#term-hashable) 如果 *object* 是可hash的。即使在 *object* 已删除。如果 [`hash()`](https://www.osgeo.cn/cpython/library/functions.html#hash "hash") 只有在 *object* 已被删除，调用将引发 [`TypeError`](https://www.osgeo.cn/cpython/library/exceptions.html#TypeError "TypeError") .

弱引用支持相等的测试，但不支持排序。如果引用仍然存在，则两个引用与其引用具有相同的相等关系（无论 *回调* ）如果其中一个引用已被删除，则仅当引用对象是同一对象时，引用才相等。

这是子类类型，而不是factory函数。

`__callback__`[](https://www.osgeo.cn/cpython/library/weakref.html#weakref.ref.__callback__ "永久链接至目标")

此只读属性返回当前与weakref关联的回调。如果没有回调或weakref的引用不再存在，则此属性将具有值 `None` .

在 3.4 版更改: 增加了 [`__callback__`](https://www.osgeo.cn/cpython/library/weakref.html#weakref.ref.__callback__ "weakref.ref.__callback__") 属性。

---

**unittest**中最核心的四部分是：**TestCase**，**TestSuite**，**TestRunner**，**TestFixture**

（1）一个TestCase的实例就是一个测试用例。测试用例就是指一个完整的测试流程，包括测试前准备环境的搭建（setUp），执行测试代码（run），以及测试后环境的还原（tearDown）。元测试（unit test）的本质也就在这里，一个测试用例是一个完整的测试单元，通过运行这个测试单元，可以对某一个问题进行验证。

（2）而多个测试用例集合在一起，就是TestSuite，而且TestSuite也可以嵌套TestSuite。

（3）TestLoader是用来加载TestCase到TestSuite中的。

（4）TextTestRunner是来执行测试用例的，其中的run(test)会执行TestSuite/TestCase中的run(result)方法

（5）测试的结果会保存到TextTestResult实例中，包括运行了多少测试用例，成功了多少，失败了多少等信息。

综上，整个流程就是首先要写好TestCase，然后由TestLoader加载TestCase到TestSuite，然后由TextTestRunner来运行TestSuite，运行的结果保存在TextTestResult中，整个过程集成在unittest.main模块中。

---

![20190929201309-image.png](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/20190929201309-image.png)

---

**setUp()和tearDown()**

setUp：表示前置条件，它在每一个用例执行之前必须会执行一次 

tearDown：表示释放资源，它在每次用例执行完之后会执行一次

若想要改变setUp()在所有用例执行之前只执行一次，和tearDown()函数在所有用例执行之后只执行一次的话

1. 需要使用python中的@classmethod内置装饰器

2. 需要把函数名改为setUpClass(cls) / tearDownClass(cls)

---

**any(x)** 判断x对象是否为空对象，如果都为空、0、false，则返回false，如果不都为空、0、false，则返回true

**all(x)** 如果all(x)参数x对象的所有元素不为0、''、False或者x为空对象，则返回True，否则返回False

---

**filter()** 

**filter()** 函数用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。

`filter(function, iterable)`

- function -- 判断函数。
- iterable -- 可迭代对象。

Python2.x 中返回的是过滤后的列表, 而 Python3 中返回到是一个 filter 类。

filter 类实现了 __iter__ 和 __next__ 方法, 可以看成是一个迭代器, 有惰性运算的特性, 相对 Python2.x 提升了性能, 可以节约内存。

---

**python的闭包问题**

如果试图在一个内部函数里对外部作用域（不包括外部函数的外部作用域）的变量进行引用，内部函数就会被认为是闭包。

由于闭包本身是基于内部函数这一概念而来，所以不能在外部函数的外部作用域对内部函数进行调用，对于内部函数来说对引用外部函数作用域内的变量进行修改，将会启动解释器的屏蔽机制。

```python
>>> def Func1():
...   x = 233
...   def Func2():
...       x *=x
...       return x
...   return Func2()
... 
>>> Func1()
Traceback (most recent call last):
 File "<stdin>", line 1, in <module>
 File "<stdin>", line 6, in Func1
 File "<stdin>", line 4, in Func2
```

python3之前的解决办法

应用容器类型（list,tuple之类的）存放外部函数作用域的变量从而不会被屏蔽机制屏蔽掉，因为容器类型不是存放在栈里面python3之前的解决办法

```python
>>> def Func1():
...   x = [233]
...   def Func2():
...       x[0] *= x[0]
...       return x[0]
...   return Func2()
... 
>>> Func1()
```

python3之后的解决办法：nonlocal关键字

```python
>>> def Func1():
...   x = 233
...   def Func2():
...   nonlocal x
...       x *= x
...       return x
...   return Func2()
... 
>>> Func1()
54289
```

---

**python weakref**

《流畅的Python2015》8.6

weakref.ref()

weakref.WeakValueDictionary()

weakref.WeakKeyDictionary()

weakref.WeakSet()

weakref.proxy

---

``types`` 模块包含了标准解释器定义的所有类型的类型对象，同一类型的所有对象共享一个类型对象。你可以使用 ``is`` 来检查一个对象是不是属于某个给定类型。

types.MethodType：实例方法

types.BuiltinFunctionType：内置接口

---

**assert**

`assert expression`等价于

```
if not expression:
    raise AssertionError
```

assert 后面也可以紧跟参数：`assert expression [, arguments]`，等价于

```python
if not expression:
    raise AssertionError(arguments)
```

---

**使用timeit模块的Timer类查看代码执行的时间**

`from timeit import Timer`

`class timeit.Timer(stmt=‘pass’, setup=‘pass’, time=<time function>)`

* stmt：要测试的代码语句（statment）

* setup：执行代码的准备工作，初始化代码或构建环境导入语句，不计入时间（一般是import之类）

* timer：定时器函数，与系统有关，win32 下是 time.clock()，linux 下是 time.time()，默认

`Timer. timeit(number=1000000)`，返回执行代码的平均耗时（float类型的秒数）

* number：执行代码次数，默认1000000

`Timer.repeat(repeat=3, number=1000000)`，返回列表

* repeat：重复执行 timeit 的次数

* number：执行代码次数，默认1000000

---

`if __name__ == '__main__'` 简单的理解就是： **如果模块是被直接运行的，则代码块被运行，如果模块是被导入的，则代码块不被运行**。

---

**python装饰器**

---

**functools.wraps**

对于受到封装的原函数来说，装饰器能够在那个函数执行前或者执行后分别运行一些代码，使得可以再装饰器里面访问并修改原函数的参数以及返回值，以实现约束定义、调试程序、注册函数等目标。装饰器一般返回一个包装器（wrapper），而functools.wraps就是**装饰包装器的装饰器**。

---

**setdefault()**

Python 字典 setdefault() 函数和 get()方法 类似, 如果键不存在于字典中，将会添加键并将值设为默认值。

```python
dict.setdefault(key, default=None)
```

- key -- 查找的键值。
- default -- 键不存在时，设置的默认键值。

如果字典中包含有给定键，则返回该键对应的值，否则返回为该键设置的值。

---

在if-elif-else、for-else、while、try-except\try-finally等关键字的语句块中并不会产成作用域。看下面的代码：

```
if True:    variable = 100
    print (variable)
print ("******")
print (variable)
```

代码的输出为：

```
100
******100
```

所以，可以看到，虽然是在if语句中定义的variable变量，但是在if语句外部仍然能够使用。

---

**sorted多条件排序**

sorted 内部实现使用了归并排序，而归并排序是稳定的排序，就是说当元素比不出大小时，其相对位置是不变的。

`sorted(iterable, key, reverse)`

key参数，接收一个函数地址，用来设置排序条件，这里我们经常使用匿名函数。

iterable的每一个元素作为参数传入key函数，key函数的返回值就是排序依据，**当返回值为一个元祖时，这个元祖中的多个元素即为多个排序条件，从前到后重要程度依次降低。**

---

append extend

---

cmp() 函数已经“离开”了，如果你真的需要 cmp() 函数，你可以用表达式 (a > b) - (a < b) 代替 cmp(a,b)。



---

super(*类*名, self).*方法*名(*方法*参数)

---

**python作用域**

[https://foofish.net/python-legb.html](https://foofish.net/python-legb.html)

LEGB规则: 查找一个名称的顺序为：local-->enclosing-->global-->builtin。

python中，类、函数、模块会产生一个作用域，代码块不会，所以 if 代码块里面的代码和外面是是同一个作用域。

---

**打印调用栈**

```python
import traceback
traceback.print_stack()
```

---

**打印函数参数**

```python
print(func.__code__.co_varnames)
```


