# Python 笔记

记录自己觉得不熟或者不会的知识点。链接地址：

https://docs.python.org/zh-cn/3/tutorial/controlflow.html

### str

Str*n 的意思是将这个字符串重复 n 次

```py
def func(*args,**arguments) :
	pass
	
""" args 必须在 arguments 参数的前面， arguments 接收 字典 (dict) 类型数据 """
""" 字符串前面带一个 r 表示原样输出 """
```



## 数据结构

列表推导：

```python
matrix = [
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12],
]
[[row[i] for row in matrix] for i in range(4)]

```



元组（元组是不可变的）：

```python
empyt=()
t = 123,456,'hello!'
```

集合（不重复的元素）：

```python
basket = {'apple','orange','apple'} # # unique letters in a
a = set('abracadabra')
b = set('alacazam')
a - b #  letters in a but not in b
a & b # letters in both a and b
a ^ b #  letters in a or b but not both

# 列表推导
a = {x for x in 'abracadabra' if x not in 'abc'}

```

字典(dict)

```python
tel = {'jack': 4098, 'sape': 4139} # init dict
dict({'sape': 4139,'guido': 4127})  # init dict
{x : x**2 for x in (2,4,6)} # 列表推导
tel['guido'] = 4127 # add element
tel['jack'] # read
del tel['sape'] # delete 
list(tel) # 列出所有的 Key
'jack' in tel # True
'jacks' not in tel # False


```



循环技巧

字典中，iterms() 可以取出键值对

```python
knights = {'gallahad': 'the pure', 'robin': 'the brave'}
for k,v in knights.iterms():
  	print(k,v)
```

Enumerate(list) 可以取出索引位置及对应的值

```python
for i,v in enumerate(['tic','tac','toe']):
  	print(i,v)
```

当同时在两个或者多个列表循环时，可以使用 zip()将函数一一匹配：

```python
questions = ['name', 'quest', 'favorite color']
answers = ['lancelot', 'the holy grail', 'blue']
for q,a in zip(questions,answers):
  print('what is your {0}? It is {1}'.format(q,a))
```

当逆向循环一个序列时，先正向定位序列，然后调用 [`reversed()`](https://docs.python.org/zh-cn/3/library/functions.html#reversed) 函数

```python
for x in reversed(range(1,10,2)):
  print(x)
```

如果要按某个指定顺序循环一个序列，可以用 [`sorted()`](https://docs.python.org/zh-cn/3/library/functions.html#sorted) 函数，它可以在不改动原序列的基础上返回一个新的排好序的序列

```python
basket = ['apple', 'orange', 'apple', 'pear', 'orange', 'banana']
for f in sorted(set(basket)):
    print(f)


```

## 模块

为了让一个目录被识别为包，当前目录下必须包含 `__init__.py` 文件。一般情况下只需要一个空的文件夹即可，如果需要在使用 ` import yourmodule from * ` 导入指定包或者加快搜索，可以在 `__init__.py`里添加一个`__all__` 列表，指明模块。



## 输入输出

如果要[格式字字符串字面值](https://docs.python.org/zh-cn/3/tutorial/inputoutput.html#tut-f-strings) 可以在字符串之前加上 `f` 或者 `F` 。

```python
year = 2016
event = 'Referendum'
f'Results of the {year} {event}'
import math
print(f'The value of pi is approximately {math.pi:.3f}.')
```

[`str()`](https://docs.python.org/zh-cn/3/library/stdtypes.html#str) 函数是用于返回人类可读的值的表示，而 [`repr()`](https://docs.python.org/zh-cn/3/library/functions.html#repr) 是用于生成解释器可读的表示（如果没有等效的语法，则会强制执行 [`SyntaxError`](https://docs.python.org/zh-cn/3/library/exceptions.html#SyntaxError)）对于没有人类可读性的表示的对象， [`str()`](https://docs.python.org/zh-cn/3/library/stdtypes.html#str) 将返回和 [`repr()`](https://docs.python.org/zh-cn/3/library/functions.html#repr) 一样的值。



在 `':'` 后传递一个整数可以让该字段成为最小字符宽度。这在使列对齐时很有用。:

```python
table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 7678}
for name, phone in table.items():
    print(f'{name:10} ==> {phone:10d}')

```



## 处理异常

```python
while True:
    try:
        x = int(input("Please enter a number: "))
        break
    except ValueError:
        print("Oops!  That was no valid number.  Try again...")
        
 for arg in sys.argv[1:]:
    try:
        f = open(arg, 'r')
    except OSError:
        print('cannot open', arg)
    else:
        print(arg, 'has', len(f.readlines()), 'lines')
        f.close()

```

抛出异常：

```python
raise NameError('HiThere') # 如果 raise 后面什么也不跟，则重新抛出已捕捉的异常

```

在 try 后面接 else 表示没有发生异常时执行操作(异常捕获后没有抛出，返回到 try 继续向下走时，也会执行 else ) 。finally 则与 java 类似



`with open("myfile.txt") as f: ` 能够自动关闭文件



## 类

请注意 *局部* 赋值（这是默认状态）不会改变 *scope_test* 对 *spam* 的绑定。 [`nonlocal`](https://docs.python.org/zh-cn/3/reference/simple_stmts.html#nonlocal) 赋值会改变 *scope_test*对 *spam* 的绑定，而 [`global`](https://docs.python.org/zh-cn/3/reference/simple_stmts.html#global) 赋值会改变模块层级的绑定。

### 类定义

```python
class Dog:

    tricks = []             # mistaken use of a class variable

    def __init__(self, name):
        self.name = name

    def add_trick(self, trick):
        self.tricks.append(trick)
```

tricks 相当于 java 类的静态变量，属于所有实例共享， name 相当于实例变量，实例之间不共享。

*数据属性会覆盖名称相同的方法属性* 

### 继承

```python
class DerivedClassName(BaseClassName):
    <statement-1>
    .
    .
    .
    <statement-N>
```

调用基类方法：即调用 `BaseClassName.methodname(self, arguments)`。

Python有两个内置函数可被用于继承机制：

- 使用 [`isinstance()`](https://docs.python.org/zh-cn/3/library/functions.html#isinstance) 来检查一个实例的类型: `isinstance(obj, int)` 仅会在 `obj.__class__` 为 [`int`](https://docs.python.org/zh-cn/3/library/functions.html#int)或某个派生自 [`int`](https://docs.python.org/zh-cn/3/library/functions.html#int) 的类时为 `True`。
- 使用 [`issubclass()`](https://docs.python.org/zh-cn/3/library/functions.html#issubclass) 来检查类的继承关系: `issubclass(bool, int)` 为 `True`，因为 [`bool`](https://docs.python.org/zh-cn/3/library/functions.html#bool) 是 [`int`](https://docs.python.org/zh-cn/3/library/functions.html#int) 的子类。 但是，`issubclass(float, int)` 为 `False`，因为 [`float`](https://docs.python.org/zh-cn/3/library/functions.html#float) 不是 [`int`](https://docs.python.org/zh-cn/3/library/functions.html#int) 的子类。

### 多重继承

```python
class DerivedClassName(Base1, Base2, Base3):
    <statement-1>
    .
    .
    .
    <statement-N>
```



最简单的情况下，你可以认为搜索从父类所继承属性的操作是深度优先、从左至右的，当层次结构中存在重叠时不会在同一个类中搜索两次。

*约定：带有一个下划线的名称 (例如 `_spam`) 应该被当作是 API 的非仅供部分*

### 迭代器

定义一个 [`__iter__()`](https://docs.python.org/zh-cn/3/reference/datamodel.html#object.__iter__) 方法来返回一个带有 [`__next__()`](https://docs.python.org/zh-cn/3/library/stdtypes.html#iterator.__next__) 方法的对象。 如果类已定义了 `__next__()`，则 [`__iter__()`](https://docs.python.org/zh-cn/3/reference/datamodel.html#object.__iter__) 可以简单地返回 `self`。

```python
class Reverse:
    """Iterator for looping over a sequence backwards."""
    def __init__(self, data):
        self.data = data
        self.index = len(data)

    def __iter__(self):
        return self

    def __next__(self):
        if self.index == 0:
            raise StopIteration
        self.index = self.index - 1
        return self.data[self.index]
      
```



### 生成器

```python
def reverse(data):
    for index in range(len(data)-1, -1, -1):
        yield data[index]
```

可以用生成器来完成的操作同样可以用前一节所描述的基于类的迭代器来完成。 但生成器的写法更为紧凑，因为它会自动创建 [`__iter__()`](https://docs.python.org/zh-cn/3/reference/datamodel.html#object.__iter__) 和 [`__next__()`](https://docs.python.org/zh-cn/3/reference/expressions.html#generator.__next__) 方法。

生成器表达式：

```python
sum(i*i for i in range(10))                 # sum of squares

```





## 标准库简介

一定要使用 `import os` 而不是 `from os import *` 。这将避免内建的 [`open()`](https://docs.python.org/zh-cn/3/library/functions.html#open) 函数被 [`os.open()`](https://docs.python.org/zh-cn/3/library/os.html#os.open) 隐式替换掉，它们的使用方式大不相同。

