---
title: Python中的可迭代对象、迭代器和生成器的异同点
tags: Python
category: Code
abbrlink: 37a38fdd
---

# Python中的可迭代对象、迭代器和生成器的异同点

解释之前，我们先来看一张概览图，下面是一张关于容器(container)、可迭代对象(Iterable)、迭代器(iterator)、生成器(generator)、生成器函数和生成器表达式的概览图。

<!--more-->

![11](https://img-blog.csdnimg.cn/20190116151114830.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NMX1dvcmxk,size_16,color_FFFFFF,t_70)
它们之间的从属关系图如下：![在这里插入图片描述](https://img-blog.csdnimg.cn/20190117002732819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NMX1dvcmxk,size_16,color_FFFFFF,t_70)

## 一、容器(container)

【概述】：**容器就是**一个用来存储多个元素的**数据结构**
【特点】：
①容器中的元素可通过迭代获取。
**②所有容器中的元素**被**存储**在**内存**中。
【举例】：以下都是常见容器对象，他们都可以通过迭代获取，所以它们也是**可迭代对象(Iterable)**，我们暂且理解可迭代对象就是可以被迭代获取的对象。(注：并非所有容器对象都是可迭代对象)
首先引入如下模块：

```js
from collections import Iterable  # 可迭代对象
from collections import Iterator  # 迭代器
12
```

**1.列表(list)**

```js
title = ['Python','Java','C++']
for i in title:            # 通过for循环，迭代获取
    print(i)               
isinstance(title,Iterable) # True
1234
```

打印顺序：Python，Java，C++，True
**2.元组(tuple)**

```js
title = ('Python','Java','C++')
for i in title:            # 通过for循环，迭代获取
    print(i)
isinstance(title,Iterable) # True
1234
```

打印顺序：同上述列表
**3.字典(dict)** （注：此处打印的是字典中的key而不是value）

```js
title = {'Python':3,'Java':1.9,'C++':2}
for key in title:            # 通过for循环，迭代获取
    print(key)
isinstance(title,Iterable) # True
1234
```

打印顺序：同上述列表
**4.集合(set)**

```js
title = {'Python','Java','C++'}
for i in title:            # 通过for循环，迭代获取
    print(i)
isinstance(title,Iterable) # True
1234
```

打印顺序：C++，Java，Python，True
**5.字符串(str)**

```js
str = 'abc'
for i in str:            # 通过for循环，迭代获取
    print(i)
isinstance(str,Iterable) # True
1234
```

打印顺序：a，b，c，True

## 二、可迭代对象(Iterable)

【简述】：**可迭代对象就是**可以**被迭代获取**的**对象**。
【特点】：Iterable定义了**可返回迭代器的__iter__()方法**。
【举例】：

```js
title = ['Python','Java','C++'] # 列表是一个可迭代对象
isinstance(title,Iterable)      # True
a = iter(title) # 由可迭代对象的iter方法返回一个迭代器
>>> next(a)
Python
>>> next(a)
Java
>>> next(a)
C++ 
>>> next(a)  # 抛出StopIteration异常
12345678910
```

如果我们写出以下代码：

```js
x = [1, 2, 3]
for elem in x:
    ...
123
```

则实际内部运行过程是这样的，列表`x`是一个可迭代对象，在`for`循环中经过`iter()`方法变为迭代器，然后遍历`x`实际就是内部调用`elem = next(x)`。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190116171200984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NMX1dvcmxk,size_16,color_FFFFFF,t_70)

## 三、迭代器(Iterator)

【简述】：迭代器是一个**带状态**的对象。之所以说是带状态的对象是因为迭代器内部持有一个状态，该状态用于**记录当前迭代所在的位置**，以方便下次迭代的时候获取正确的元素。迭代器可以通过next()方法来迭代获取下一个值。
**【特点】：①Iterator实现了__iter__()和__next__()方法。②迭代器不会一次性把所有元素加载到内存，而是需要的时候才生成返回结果**(不同于容器)。
【举例】：和上面例子一样，下面的`a`就是一个迭代器，和可迭代对象`title`不同，`a`可以通过`next(a)`来逐个获取其中的每个元素。

```js
title = ['Python','Java','C++'] # 列表是一个可迭代对象
isinstance(title,Iterable)      # True
a = iter(title) # 由可迭代对象的iter方法返回一个迭代器
>>> next(a)
Python
>>> next(a)
Java
>>> next(a)
C++ 
>>> next(a)  # 抛出StopIteration异常
12345678910
```

如果使用`next(title)`则会报`TypeError`错，显示列表对象不是一个迭代器。

```js
>>> next(title)  
TypeError: 'list' object is not an iterator
12
```

迭代器每次调用next()方法的时候做两件事：

1. 为下一次调用next()方法**修改状态**
2. **生成**当前调用的**返回结果**

## 四、生成器(generator)

【概述】：生成器(generator)是一种特殊的迭代器。
【特点】：
①生成器拥有迭代器的**迭代传出数据**的功能，但用关键字`yield`来**替换**迭代器中的__next__()方法来实现，而拥有`yield`关键字的函数就是生成器函数。
②生成器可以**传入数据**进行计算(不同于迭代器)，并根据变量内容计算结果后返回。
③迭代器不会一次性把所有元素加载到内存，而是调用的时候才生成返回结果(相同于迭代器，不同于容器)。
④可以通过`for`循环进行迭代(因为生成器是迭代器)
**综上所述：生成器是迭代器的衍生物，但迭代器不是生成器，因为迭代器没有传入数据功能。**

### 4.1 生成器函数(generator function)

【概述】：含有`yield`关键字的函数就是生成器函数。
【举例】：
1.生成器可以通过`for`循环迭代，这一点**和迭代器中的next()函数功能相同**，如下我们使用一个**有限序列**作为例子，如果是**无限序列**，则会无休止的循环下去

```js
def d():
    yield 1
    yield 2
    yield 3
    yield 4
for i in d():
    print(i)
type(d()) # generator
isinstance(d(),Iterator) # True
isinstance(d(),Iterable) # True
12345678910
```

得到如下结果：（也可以看出生成器即是迭代器，也是可迭代对象）

```js
1
2
3
4
generator
12345
```

2.以下我们展示一个**传入和传出数据**的例子。

```js
def d():
    print('初始化')
    sum = 0
    value = yield sum
    sum = sum + value
    print('sum的值是：%d' % sum)
    value = yield sum
    sum = sum + value
    print('sum的值是：%d' % sum)
    value = yield sum
    sum = sum + value
    print('sum的值是：%d' % sum)
    return sum+1
c = d()          # c是一个生成器，此行代码并不运行d()内容
a = c.send(None) # 启动生成器，遇到d()的第一个yield时中断
print('生成器传出的值:%d' % a)
a = c.send(1)
print('生成器传出的值:%d' % a)
a = c.send(1)
print('生成器传出的值:%d' % a)
1234567891011121314151617181920
```

`yield`有中断的功能:

当运行`a = c.send(None)`时，启动生成器函数，在第一个`yield`中断，此时这行程序仅仅运行了`yield sum`并没有开始赋值，而`yield sum`就相当于`return sum`，即向函数外传出`sum`，所以函数外接收值的变量`a`存储的值是`0`。

当运行`a = c.send(1)`时，我们继续启动生成器函数开始运行`value = yield`，并向生成器函数的第一个中断点`yield`传递了值`1`，然后通过`yield`把`1`传递给了`value`并通过后续计算累加sum。程序直到第二个`yield`中断，向函数外返回第二个`sum`。以此类推。

所以执行结果如下：

```js
初始化
生成器传出的值:0
sum的值是：1
生成器传出的值:1
sum的值是：2
生成器传出的值:2
123456
```

【注】：
1）第一个`send(None)`填入的参数必须是`None`，因为在启动生成器函数到第一次中断，程序只运行到第一个`yield sum`，没有赋值语句，所以只能填`None`。
2）对于生成器函数最后的`return sum`语句并不向函数外传递`sum`，而是会在迭代结束时报错`StopIteration: 3`，返回值`sum`包含在`StopIteration`的value中，也就是`3`，可以捕获`StopIteration`在函数外得到这个值。把上面代码中的`a = c.send(None)`之后的代码改成如下代码即可。

```js
while True:
	print('生成器传出的值:%d' % a)
    try:  
        a = c.send(1)
    except StopIteration as e:
        print('生成器传出最后的值:%d' % e.value)
        break
1234567
```

如果不加返回值的话直接`break`就行，没有外循环的话就直接`pass`。

```js
while True:
	print('生成器传出的值:%d' % a)
    try:  
        a = c.send(1)
    except StopIteration:
        break
123456
```

### 4.2 生成器表达式(generator expression)

生成器表达式是列表生成式的生成器版本，看起来像列表生成式，但是它返回的是一个生成器对象而不是列表对象。当然，它既然是生成器，也就可以上个例子一样通过`send()`函数来迭代。

```js
>>> [x*x for x in range(10)] # 列表生成式
[0, 1, 4, 9, 16]
>>> (x*x for x in range(10)) # 生成器表达式
<generator object <genexpr> at 0x401f08>
1234
```

## 总结：

| 对象      | 特点                                                         | 功能                            |
| --------- | ------------------------------------------------------------ | ------------------------------- |
| Iterable  | 实现`__iter__()`方法                                         | 返回一个Iterator                |
| Iterator  | ①实现`__iter__()`和`__next__()`方法 ②数据需要的时候才存储到内存中 | 逐个传出数据                    |
| generator | ①通过`yield`替换迭代器两个方法并有中断功能 ②数据需要的时候才存储到内存中 | ①逐个传出数据 **②逐个传入数据** |
| container | ①容器中的元素可通过迭代获取 ②所有容器中的元素被存储在内存中  | ——                              |

## 编一则小故事：

在Python中有一种可迭代对象(Iterable)，他声称自己可以通过迭代来获取值(如for循环)，但怎么迭代他说那是迭代器(Iterator)的事，他只负责把自己变成迭代器。于是当他揽下一个需要迭代的活时，他就通过Iter()把自己变成了迭代器。

另外一个容器兄弟开始呼应：”我就是可迭代对象呢，我包含常见的列表、元组、字典、集合和字符串，我将这些序列存储在内存中，需要的时候可以一并取出“

迭代器有点不屑，开始怼容器说：“你将所有序列都存储在内存中，对于少量有限序列是可以的，那如果是大量序列或无限序列，内存都让给你存岂不是很败家？我是迭代功能的实干家，我可以通过next()进行迭代,但并不是把所有序列放在内存中再迭代取值，而是仅仅将迭代到的某个值取到内存中，做到按需存储。

生成器对迭代器说：“我就是另一个你，但我们不一样。大兄弟你的确是个实干家，但你仅仅只能迭代取出数据，而我除了有你的功能还可以通过send()传入数据，传入的数据可在生成器内进行计算呢。”

最后，生成器和迭代器和容器说：“我们都是可迭代对象，Iterable对于我们来说更像一种特点，表示我们是可迭代的，但是功能的实现是我们自己完成的”


另，对`yield`关键字还不理解的，可参考这篇文章：[深入理解yield](http://www.cnblogs.com/coderzh/articles/1202040.html)



本文转载至[博文](https://blog.csdn.net/SL_World/article/details/86507872)。