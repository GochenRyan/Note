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
