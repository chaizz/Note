---
title: 面试-Python
author: chaizz
date: 2021-11-15 21:28:48
tags: 面试
categories:
    - 面试
    - Python
photos: ["https://origin.chaizz.com/037cabda46cf11ec9d7c5254006b8f1d.png"]
cover: "https://origin.chaizz.com/037cabda46cf11ec9d7c5254006b8f1d.png"
---

​                

<!--more-->

#  一、基础试题

## 1、对字典`d = {'a': 24, 'b': 53, 'd': 56, 'h': 7}` 按照字典中的value值排序。

```python
# 按照字典中的键排序：
sorted(d.items(), key=lambda x: x[1])

# 按照字典中的键排序：
sorted(d.items(), key=lambda x: x[0])

# 在转化为字典 使用dict 函数
```

## 2、反转字符串 "aSter"

```python
print('aSter'[::-1])

# 取头不取尾
```

## 3、按照list1 中的元素的由从大到小排序

```python
list1 = [{'name': "b", 'age': 20}, {'name': "a", 'age': 10}, {'name': "d", 'age': 50}, {'name': "c", 'age': 7}]

sorted(list1, key=lambda x: x['age'], reverse=True)

# reverse=True  由大到小 sorted排序默认由小到大
```

## 4、常用的字符串格式化有哪些？

```python
name = "张三"
# 1.占位符 %s
str = "%s，你好!" % name
# 2. format
str2 = "{}， 你好!".format(name)
print(str2)
```

## 5、下面代码的输出结果是什么 ？

```python
list = ['a', 'b', 'c', 'd', 'e'] 

print(list[10:])   

[]
```

输出结果是空 不会产生IndexError错误，尝试用超出成员个数的Index来获取某个列表的成员。

## 6、写一个列表生成式产生一个等差为11的等差数列。

```python
print([x*11 for x in range(10)])
```

## 7、给定两个列表怎么找出他们相同的元素和不同的元素

```python
list1 = [1, 2, 3, 4]
list2 = [3, 1, 5, 6]

set1 = set(list1)
set2 = set(list2)
print(set1 & set2)
print(set1 ^ set2)
```

## 8、python代码实现删除一个list的重复的元素

```python
list1 = ['a', 'v', 'a', 'c', 'd', 'f', 'd', 'v', 'r', 's', 'r']
print(set(list1))
```

## 9、举字符串、列表、元祖、字典的五个常用的用法。

```python
# 字符串：replace、strip、split、reverse、upper、lower、join

# 列表：append、pop、insert、remove、count、index

# 元祖：index、count、len()、dir()

# 字典：get、keys、values、popitems、clear、uodate、items
```



## 10、什么是反射 ？ 以及他的应用场景。

反射就是通过字符串的形式去对象中访问或操作这个未知的属性或变量，是一种基于字符串的事件驱动。

在面向对象中把对象能够访问、查询、修改自身的状态或者行为称之为反射。

在python中，可以通过字符串的的形式来操作对象的属性。这种行为称之为python中的反射。

**python** **实现反射的手段：**

是通过四个内置函数来实现：**hasattr(object,name)** **getattr(object,name,default=None) setattr(x,y,v) delattr(x,y)**

判断对象中是否有这个方法或变量

```python
class Person(object):
  def __init__(self,name):
    self.name = name
  def talk(self):
    print("%s正在交谈"%self.name)
```



```python
p = Person("laowang")    

print(hasattr(p,"talk"))  # True。因为存在talk方法

print(hasattr(p,"name"))  # True。因为存在name变量

print(hasattr(p,"abc"))   # False。因为不存在abc方法或变量
```



**反射的好处 ：**

实现可插拔机制、动态导入模块（基于反射原理，获取当前的模块的成员）

## 11、简述Python的深浅拷贝 ，详细见[链接](https://zhuanlan.zhihu.com/p/54011712)。

1.外层添加元素时，浅拷贝不会随原列表变化而变化；内层添加元素时，浅拷贝才会变化。
2.无论原列表如何变化，深拷贝都保持不变。
3.赋值对象随着原列表一起变化

```python
# copy() ： 浅拷贝，仅仅拷贝数据集合的第一层

# deepcopy() : 深拷贝，拷贝数据集合的所有层

import copy

a=[1,2,3,{1:2}]

print("=====赋值=====")

b=a

print(a)

print(b)

print(id(a))

print(id(b))

print("=====浅拷贝=====")

b=copy.copy(a)

print(a)

print(b)

print(id(a))

print(id(b))

print("=====深拷贝=====")

b=copy.deepcopy(a)

print(a)

print(b)

print(id(a))

print(id(b))

#结果：

=====赋值=====

[1, 2, 3, {1: 2}]

[1, 2, 3, {1: 2}]

2145919592448

2145919592448

=====浅拷贝=====

[1, 2, 3, {1: 2}]

[1, 2, 3, {1: 2}]

2145919592448

2145919592320

=====深拷贝=====

[1, 2, 3, {1: 2}]

[1, 2, 3, {1: 2}]

2145919592448

2145919532928
```

## 12、Python的垃圾回收机制。[网页地址](https://zhuanlan.zhihu.com/p/83251959)

1. **引用计数**

在python中每一个对象的的核心就是一个结构体PyObject，他的内部有一个引用计数器（（ob_refcnt）），程序在运行的过程中他会实时的更新 引用计数器（ob_refcnt）的值，来反映当前对象的名称数量，当某个对象的引用计数为零的时候，那么他的内存就会被释放掉。

导致引用计数加一的情况有 ：对象被创建、对象被引用、对象被作为参数传入一个函数中、对象存储在容器中。

导致引用计数减一的情况有：对象别名被显示销毁 del、对象别名被赋予新的对象、一个对象离开他的作用域、对象所在的容器被销毁或者是从容器中删除对象 。

我们可以通过sys包中的getrefcount()来获取一个名称所引用的对象当前的引用计数（注意，这里getrefcount()本身会使得引用计数加一）。  

```python
sys.getrefcount(a)
```

**引用计数的优点**：

高效、实现逻辑简单、具备实时性，一旦一个对象的引用计数归零，内存就直接释放了。

**引用计数的缺点：**

逻辑简单，但实现有些麻烦。每个对象需要分配单独的空间来统计引用计数，这无形中加大的空间的负担，并且需要对引用计数进行维护，在维护的时候很容易会出错。

可能会比较慢。正常来说垃圾回收会比较平稳运行，但是当需要释放一个大的对象时，比如字典，需要对引用的所有对象循环嵌套调用，从而可能会花费比较长的时间。

循环引用。这将是引用计数的致命伤，引用计数对此是无解的，因此必须要使用其它的垃圾回收算法对其进行补充。

2. **标记-清除**

它是解决容器对象（(注意，只有容器对象才会产生循环引用的情况，比如列表、字典、用户自定义类的对象、元组等。而像数字，字符串这类简单类型不会出现循环引用。作为一种优化策略，对于只包含简单类型的元组也不在标记清除算法的考虑之列)）可能产生的循环引用的问题。不改动真实的而引用计数，而是将引用计数复制一份副本，改动该对象引用的副本，对于副本做得任何改动都不影响生命对象整体的维护。

**标记清除的步骤：**

标记阶段：GC会把所有活动对象打上标记，这些活动对象就像是一个点，他们之间使用引用关系来连接，最终每个点和边构成了一个有向图。

![](https://tc.chaizz.com/8d3dc5ea460a11ec9d7c5254006b8f1d.png)

GC从跟对象触发遍历所整个图，如果该对象是可达的（reachable）也就是说还有对象在引用他，那么就标记该对象可达。如上图中从根对象开始遍历1、2、3、4是可达的，5、6、7是不可达的。（整个根对象就是全局对象，调用栈，寄存器）

清除阶段：遍历的对象不可达，就将其回收。通过分代回收来加速清理对象。

3. **分代回收**

在循环引用对象的回收中，整个应用程序会被暂停，为了减少应用程序暂停的时间，Python 通过**“分代回收”(Generational Collection)**以空间换时间的方法提高垃圾回收效率。

分代回收是基于这样的一个统计事实，对于程序，存在一定比例的内存块的生存周期比较短；而剩下的内存块，生存周期会比较长，甚至会从程序开始一直持续到程序结束。生存期较短对象的比例通常在 80%～90% 之间，这种思想简单点说就是：对象存在时间越长，越可能不是垃圾，应该越少去收集。这样在执行标记-清除算法时可以有效减小遍历的对象数，从而提高垃圾回收的速度。

分代回收 根据内存中对象的存活时间将他们分为三代，新生的对象放入0代，如果一个对象能在0代的垃圾回收机制中存活下来，GC就会将它放入1代中，如果1代的对象在1代的垃圾 回收过程中存货下来，则会进入二代。

分代回收的触发机制：

```python
import gc
print(gc.get_threshold())
```

以上代码执行结果是一个元祖：(700, 10, 10)

- 当分配对象分个数减去释放对象的个数差值大于700时，就会产生一次0代回收。
- 10次0代回收后进行一次1代回收。
- 10次1代回收后进行一次2代回收。

对于0代的对象他们有可能只会被使用一次，所以需要被经常回收。经过一轮回收之后他们是那些使用比较频繁的对象，而且他们已经存活了很久的时间，大概率还会存活更久，因此二代的会后就不会那么频繁。

可以通过(700, 10, 10) 这三个值进行更改分代回收触发的条件。



**总体来说，在Python中，主要通过引用计数进行垃圾回收；通过 “标记-清除” 解决容器对象可能产生的循环引用问题；通过 “分代回收” 以空间换时间的方法提高垃圾回收效率。**

## 13、如何打乱一个排好序的lisit对象

```python
import random

alist = [1, 2, 3, 4, 5, 6, 7]

random.shuffle(alist)

print(alist)
```

## 14、从0-99这100个数中随即取出十个数字 要求不能重复。

```python
import random

print(random.sample(range(0,99),k=10))
```



## 15、Python如何捕获异常、处理异常 、抛出异常

捕获异常：

```python
try:
  pass
except Exception as e:
  pass
```

处理异常：

```python
try:
  pass
except Exception as 
  pass
finally:
  pass

#else:不发生异常执行的语句
#finally：无论是否发生异常都执行的语句
```



```python
# raise Exception  抛出异常
```

## 16、python递归的大层数

最大递归为998

修改最大递归值：

```python
import sys
sys.setrecursionlimit(1000)
```

## 17、列表推导式和生成器表达式`[i % 2 for i in range(10)] `和 `(i % 2 for i in range(10)) `输出的结果分别是什么？

```python
print([i%2 for i in range(10)])

\>>> [0, 1, 0, 1, 0, 1, 0, 1, 0, 1]

print((i%2 for i in range(10)))

\>>> <generator object <genexpr> at 0x000002AD4E8F09E0>
```

## 18. 什么是闭包？

指的是定义在一个函数内部的函数 ，被外层函数包裹着。其特点是可以访问到外层函数的名字。 闭包有两种不同的方式，第一种是在函数内部就直接调用了；第二种是返回一个函数名称。

```python
# 第一种形式 在外层函数中直接调用内层函数

def mark(name):
  num = 100
  def func(weight):
​    weight += 1
​    print(name, weight)
  func(100)
    
mark('塞拉斯')
```



```python
# 第二种形式:在外层函数中返回内层函数对象    （也就是装饰器）

def maker(name):
  num = 1
  def func(height):
    height += 1
    print(name, num, height)
  return func

a = maker('张三')
a(999)
```



 “闭包”的作用——保存函数的状态信息，使函数的局部变量信息依然可以保存下来。

```python
def Maker(step): # 包装器
    num = 1
    def fun1(): # 内部函数
        # nonlocal关键字的作用和前面的local是一样的，如果不使用该关键字，则不能在中内部函数改变“外部变量”的值
        nonlocal num
        # 改变外部变量的值（如果只是访问外部变量，则不需要适用nonlocal）
        num = num + step
        print(num)
    return fun1


j = 1
# 调用外部包装器
func2 = Maker(3)
while (j < 5):
    # 调用内部函数4次 输出的结果是 4、7、10、13
    func2() 
    j += 1
```



>  这就是“闭包”的最大的作用——保存局部信息不被销毁。

## 19、字典推导式

```python
a = ['a', 'b', 'c', 'd', 'e']
b = [1, 2, 3, 4, 5]
d = {k: v for k, v in zip(a, b)}
{'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}
```

## 20、python2和python3的差别

新增的库 asyncio 内置库 asybs/await 原生协程支持异步编程。

Enum(枚举) mock、asyncio、ipaddress

print表达式 改为了方法 print（）

raw_input （） 改为input （）

Python3中的Str类型代表Unicode字符串，Python2中的Str类型代表bytes字节序列。

Python3中的 / 返回浮点数，Python2中根据结果而定，能被整除返回整数，否则返回浮点数

## 21、文件操作 xreadlines 和Readlines 的区别

readlines（）是把文件全部读取到内存，并解析成一个list ,当文件体积过大时要占用很多内存。

xreadlines（） 则是返回一个iter() file 迭代器，在python2.3之后，已经不推荐这样使用了，而是直接for循环迭代文件对象。

最好的方式是使用 with open as f，对可迭代对象 f，进行迭代遍历：for line in f，会自动地使用缓冲IO（buffered IO）以及内存管理，而不必担心任何大文件的问题。

## 22、列举布尔值为False 的常见的值？

```python
0、[]、（）、{}、””、False、 None
```



## 23、匿名函数lambda 的用法。

Lambda 作为一个表达式，定义了一个匿名函数。如果使用lambda，lambda内不要包含循环 。lambda 是为了减少单行函数的定义而存在的。

## 24、Python类中__init__（self） 和 __new__(cls)的区别：

__init__(self) ：负责初始化__new__(cls)创建的对象，是一个对象方法。接受self参数，以及其他初始化的参数 

__new__(cls) ：创建__init__(self)初始化的对象。是一个类方法。在调用__new__(cls)之前连对象都没有。只接受一个参数 cls，必须要有返回值，返回实例化的对象。

## 25、列出5个python标准库

```python
# 标准库：sys、os、re、urllib、logging、datetime、random、threading、multiprocessing、base64

# 第三方库：requests、Scrapy、gevent、pygame、pymysql、pymongo、redis-py、Django、Flask、Werkzeug、celery、IPython、pillow
```

## 26、python中生成随机整数、随机小数、0--1之间小数方法

```python
import random
import numpy as np

# 随机整数
print(random.randint(0, 99999))

# 随机小数
print(np.random.randn())

# 随机 0-1 小数
print(random.random())
```





## 27、type和instance的区别

type用于获取对象的类型，返回的是对象的类型

instance用于测试对象是否属于某种类型或者多种类型之一，返回的是布尔类型

type不能判断子类对象是否属于父类，instance能够判断



## 28、python 实现装饰器

通用装饰器

使用场景：常见的认证。权限控制、日志打印、函数执行耗时等

```python
from functools import  wraps

def wrapper(func):
    @wraps(func)
    def inner(*args, **kwargs):
        ts = time.time()
        ret = func(*args, **kwargs)
        print(f'函数：{func.__name__} 耗时：{time.time() - ts}')
        return ret
    return inner

@wrapper
def sing(name):
    print(f'唱{name}')
    return 'ok'

if __name__ == '__main__':
    print(sing('儿歌'))
    
    
# 唱儿歌
# 函数：sing 耗时：0.0
# ok
```



带参数的装饰器

可以根据装饰器的参数，进行返回不同的装饰效果，或者进行流程控制。

```python
def outer(var):
    def func(func):

        @wraps(func)
        def inner(*args, **kwargs):
            ret = func(*args, **kwargs)
            print("-----------")
            return ret

        @wraps(func)
        def inner2(*args, **kwargs):
            ts = time.perf_counter()
            ret = func(*args, **kwargs)
            print(f'函数：{func.__name__} 耗时：{time.perf_counter() - ts}')
            return ret

        if var == 1:
            return inner
        else:
            return inner2

    return func


# 使用方法
@outer(1)
def dance(name):
    print(f'跳{name}')
    return 'ok'


if __name__ == '__main__':
    print(dance('恰恰'))

    
# 我是outer装饰器
# 跳恰恰
# ok

```





# 二、     数据库

## 1、什么是事务

**事务**是数据库并发控制的基本单位。相当于对一系列sql语句的集合，事务要么全部执行成功，要么全部失败。

事务的四个特性 ：ACID 

A（atomicity）：原子性：一个事务的操作要么全部完成要么全部失败。

C（consistency）：一致性：事务开始前后数据保持完整性没有被破坏。

I（isolation）：隔离性：允许多个事务同时对数据库进行读写和修改。

D（durability）：持久性：事务结束之后，修改是永久的。

 

**事务的并发控制可能产生的问题**：

幻读：一个事务进行第二次查出现第一次没有的结果。

非重复读：一个事务读取两次读到两个不同的结果。

脏读：一个事务读取到另一个事物没有提交的修改。

丢失修改：并发写入导致其中的一些修改丢失。

 

**为了解决并发控制异常，定义了四种事务的隔离级别：**

读未提交：别的事务可以读到未提交的改变。

读已提交：只能读到已提交的数据。

可重复读：同一个事务先后读取结果一样。

串行化：事务完全串行化的执行，隔离级别最高，执行效率最低。

**解决高并发情况下的插入重复：**

   使用数据库的唯一索引。

   使用队列的异步写入。

   使用redis实现分布式锁。

## 2、Mysql的数据类型：

\-   Char : 存储定长的字符串。

\-   Varchar：存储定长的字符串。

\-   Text ：存储文本比较长的类型

\-   Tinyint：一个字节，-127 到 255

\-   Int：四个字节 （）

\-   Datetime：8个字节

\-   Timestamp ：四个字节 只能存储到从1970到2038年

## 3、MYSql 的两个常用的引擎

Innodb :支持事务，支持外键、支持行锁、支持表锁。不支持全文索引。

Myisam: 不支持事务，不支持外键、只支持表锁 支持全文索引。

## 4、Mysql 索引的原理以及优化常见的问题

**索引的类型 ：**

普通索引 create index

唯一索引 create unique index

多列索引

主键索引 只有一个，全文索引 （innodb 不支持）

**那些情况下用索引：**

经常用查询需条件的字段去创建索引 （where条件）

经常用坐标链接的字段

经常用在order by group by 后面的字段

**创建索引有哪些需要注意的：**

不能非空字段上创建索引，

不要在很多字段相同的字段上创建索引

索引的长度不要太长

**索引失效的情况：**

- 对于多列索引，不是使用的第一部分，则不会使用索引（最左匹配原则）

- % Like 语句 以%开头索引失效。

- 如果列类型是字符串，那一定要在条件中将数据使用引号引用起来,否则不使用索引。（隐式转换）

- 如果条件中有or，即使其中有条件带索引也不会使用(这也是为什么尽量少用or的原因)，要想使用or，又想让索引生效，只能将or条件中的每个列都加上索引。
-  查询条件使用函数在索引列上，或者 对索引列进行运算， 运算包括(+，-，*，/，! 等) 

- 当全表扫描速度比索引速度快时，mysql会使用全表扫描，此时索引失效。

**什么是聚集索引 和非聚集索引：**

非聚集索引：数据和索引单独存储。myisam ：使用的是非聚集索引。Innodb 主键索引是聚集索引

**如何排查慢查询：**

开启慢查询日志 slow_query_log_file

通过 explain 排序索引问题。

调整数据修改索引，是不是有隐式转换。



## 5、SQL语句的编写问题

**内连接：**（Inner join）将左表和右表关联起来的数据进行返回。类似于求两个表的交集。

```mysql
Select * from a inner join b on a.id = b.id
```

**外链接：**

左连接：返回左表中的所有数据，即使右表没有匹配的数据。

```mysql
Select * from a left join b on a.id = b.id
```

右链接 ：返回右表中的所有数据，即使左表没有匹配的数据

```mysql
Select * from a right join b on a.id = b.id
```

没有匹配的字段都会自动设置为null。

## 6、MySQL为什么使用B+树数据结构而不用B树，B+树相对于B树有什么优点



是因为B树在提高了磁盘IO性能的同时并没有解决元素遍历的效率低下的问题，B+树只要遍历叶子节点就可以实现整棵树的遍历。B+树比B树多了一个双向链表，在进行范围查找的时候有更快的效率，且B+树在叶子节点冗余了上面的根节点。



## 7、MySQL事务的特性以及隔离级别

**事务的四大特性：**

原子性（atomicity）：事务的操作要么全部成功要么全部失败，是通过MySQL的undo log 来实现的。

一致性（consistency）：事务开始和结束之后不会对数据破坏，数据是符合预期的。原子性、隔离性、持久性共同实现一致性。

隔离性（isolation）：数据库允许多个事务对数据进行操作，隔离性可以放置数据库并发操作而导致数据的不一致问题。是通过LBCC+MVCC实现的。

持久性（durability）：事务结束之后对数据的改变是永久的，不会因为外界的干扰而导致数据更改。在数据库中是由redo Log 来实现的。

**事务的隔离级别：**

![](https://mmbiz.qpic.cn/mmbiz_png/sHw3tUJQYqv8YPTrg9lpFjt5WmnTicwibMqF5XDaZXJcrF9rCDmNxLp5z0gbHPzT11eQ5EyRtYBVdtkOVRQ5WABw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



## 8、MySQL什么情况下会发生死锁，如何解决死锁

**什么是死锁：**多个事务因竞争锁而造成的一种相互等待的僵局。

**出现死锁的情况：**



## 9、简述乐观锁和悲观锁的区别以及使用场景

**乐观锁：**每次获取数据的时候，不会担心数据被修改，所以每次获取数据的时候不会对数据加锁，但是在更新数据的时候会判断数据是否被修改过，如果数据被修改过，则不进行数据更新，如果没有被修改过，则进行数据更新。由于数据没有加锁，所以这期间数据有可能被其他线程进行读写操作。

**悲观锁：**每次获取数据的时候都会担心数据被修改，所以每次获取数据的时候都会加锁，确保自己在使用的过程中，数据不会被修改，使用完成后释放锁，由于数据被加锁所以其他的线程多数据进行修改需要进行等待。

**使用场景：**

悲观锁：比较适合写入的操作比较频繁的场景，如果出现大量的读取操作，每次读取的时候都会加锁，这样会增加大量的开销，降低系统的吞吐量。

乐观锁：比较适合读取操作比较频繁的场景，如果出现大量的写操作，数据发生冲突的可能性就会增加，为了保证数据的一致性，底层就会不断的重新获取数据，会增加大量的查询操作，降低了性能。

## 10、聚簇索引和非聚簇索引的区别，什么情况下使用聚簇索引

**聚簇索引：**将数据和索引存放到一起，找到索引就找到数据了。innodb引擎使用聚簇索引。

**非聚簇索引：**将数据和索引分开存放，索引结构的叶子结点存储的是数据的对应地址的指针，Myisam通过key-buffer把索引先缓存到内存中，当需要访问数据时候，在内存中直接搜索索引，然后通过素银找到对应的数据，这也就是索引不在key-buffer命中时，速度慢的原因。

## 11、脏读和幻读产生的场景，InnoDB是如何解决幻读的

**脏读：**一个事务在前后两次查询过程中得到的结果不一致由因为读取到另一个事务**未提交**的数据叫产生了脏读。它在事务隔离级别为：读未提交的情况下产生。

**幻读：**在一个事务中前后两次查询读取到了不一样的结果数，是由于读取到其他的事务已经提交的新的数据。这种情况产生的叫做幻读，读未提交，读已提交这两种事务隔离级别下都会产生。

**解决幻读：**

是通过间隙锁+锁住本身的数据（next-key），间隙锁锁住一段范围，所以其他事务无法对这段范围的数据进行插入删除等操作，所以就不存在幻读的问题。

<br> 

如果事务隔离级别是 Read Commit ，一个事务的每一次 Select 都会去查一次ReadView ，每次查询的Read View 不同，就可能会造成不可重复读或者幻读的情况。

如果事务的隔离级别是可重读，为了避免不可重读读，一个事务只在第一次 Select 的时候会获取一次Read View ，然后后面索引的Select 会复用这个 ReadView.

## 12、MySQL的主从复制的原理

MySQL主从复制涉及到三个线程，一个运行在主节点（log dump thread），其余两个(I/O thread, SQL thread)运行在从节点，如下图所示:

![](https://tc.chaizz.com/69080f8a407611ec9d7c5254006b8f1d.jpeg)

- 从节点上的I/O 进程连接主节点，并请求从指定日志文件的指定位置（或者从最开始的日志）之后的日志内容；
- 主节点接收到来自从节点的I/O请求后，通过负责复制的I/O进程根据请求信息读取指定日志指定位置之后的日志信息，返回给从节点。返回信息中除了日志所包含的信息之外，还包括本次返回的信息的bin-log file 的以及bin-log position；从节点的I/O进程接收到内容后，将接收到的日志内容更新到本机的relay log中，并将读取到的binary log文件名和位置保存到master-info 文件中，以便在下一次读取的时候能够清楚的告诉Master“我需要从某个bin-log 的哪个位置开始往后的日志内容，请发给我”；
- Slave 的 SQL线程检测到relay-log 中新增加了内容后，会将relay-log的内容解析成在祝节点上实际执行过的操作，并在本数据库中执行。



## 13、主从复制延迟产生的原因，以及如何解决？

产生延迟的原因：

- 主从机器性能差异
- 主机器有大量的写操作，从机器有大量的读操作，从而影响从机器复制的性能。
- 大事务的执行，本身事物执行消耗了大量时间



## 14、缓存的适用场景？为什么使用缓存？

\-   缓解关系数据库的并发压力，

\-   减少响应的时间

\-   提升吞吐量

## 15、Redis 和memcached 的区别：

**Redis 支持的数据类型：**

\-   String ： 实现简单的KV键值对存储，计数器

\-   List ： 双向链表，实现队列， 用户的的关注或者粉丝表表

\-   Hash ： 用来存储彼此相关信息的键值对。

\-   Set：存储不重复的元素，例如用户的关注着。

\-   Sort set ：有序集合，存储实时信息排行榜

## 16、**Redis实现持久化的方式：**

\-   快照的方式， 把数据快照放在磁盘二进制文件中，dump.rdb （Redis默认开启）

\-   AOF：每写一个命令追加到appendonly.aof 中。

\-   修改redis 配置中实现

## 17、**Redis如何实现分布式锁**

使用setnx 实现加锁，可以同时通过expire 添加超时时间。del删除锁，getset key value 先get在set 先返回key对应的值，如果没有就返回空，然后在将key设置为value。

- 获取锁的时候，使用setnx加锁，并使用expire命令为锁添加一个超时时间，超过该时间则自动释放锁，锁的value值为一个随机生成的UUID，通过此在释放锁的时候进行判断。
- 获取锁的时候还设置一个获取的超时时间，若超过这个时间则放弃获取锁。
- 释放锁的时候，通过UUID判断是不是该锁，若是该锁，则执行delete进行锁释放。

## 18、**缓存的使用模式：**

常用的有三种，

cache aside 同时更新缓存和数据库。（数据一致性的问题：解决，都是写入数据库，删除缓存。在更新缓存）Read/write through 先更新缓存。缓存负责同步的更新数据库。

write behind caching 先更新缓存，缓存定期异步更新数据库。

## 19、**如何缓解缓存穿透的问题：**

**产生的原因一：由于大量的请求查询缓存，查不到就回去数据库去取。数据库也查不到数据。（多数是由于非法攻击）**

解决：

- 对参数进行合法性校验。

- 将查不到的数据返回一个none，把none缓存下来，有新的数据插入，在把none 删除，或者设置较短的缓存时间。

- 使用布隆过滤器

### 布隆过滤器简介

本质上布隆过滤器( BloomFilter )是一种数据结构，比较巧妙的概率型数据结构（probabilistic data structure），特点是高效地插入和查询，可以用来告诉你 “某样东西一定不存在或者可能存在”。

相比于传统的 Set、Map 等数据结构，它更高效、占用空间更少，但是缺点是其返回的结果是概率性的，而不是确切的。



### 布隆过滤器原理

布隆过滤器内部维护一个位数组（bitarray），开始所有数据全部置0，当一个元素讲过多个hash函数计算不同的哈希值，并通过哈希值找到对应的bitarray，将值改为1。**（需要说明，布隆过滤器存在误判的可能）数组越长误判率越低，占用的空间也越大。）**

![](https://tc.chaizz.com/128523c846b111ec9d7c5254006b8f1d.png)

以上是一个空的布隆过滤器，现在要插入这个A字段，经过三个hash函数计算得到了2、5、7 所以将将布隆过滤器的相对应的值设置为1。

![](https://tc.chaizz.com/4c40b08646b211ec9d7c5254006b8f1d.png)

接下来继续插入B字段，计算出来的值为2、4、8，继续往布隆过滤器对相应的位置上设置为1，注意A和B同时hash计算出来的值一致。所以导致了布隆过滤器不能确保某个元素一定存在。

![](https://tc.chaizz.com/9600ff0a46b211ec9d7c5254006b8f1d.png)

布隆过滤器的查询也很简单，例如我们要找一个字段C，只需要计算出他的hash值，如果该值为2、3、4，那么因为布隆过滤器对应bit位上的数据有一个不为1，所以就断定C不存在，但是如果他计算的值为1、4、8，name就不能确定他一定存在。

因此随着添加的值越来越多，bit位的占用也就就越多，布隆过滤器的误判性也就会越来越高。如果bit位都为1的话，那就是所有的数据都存在 ，这时候布隆过滤器也就失去了过滤的功能。至此，选择一个合适的过滤器长度就显得非常重要。

### 布隆过滤器的应用

- 网页爬虫对URL的去重，避免爬取相同的URL地址
- 反垃圾邮件，从数十亿个垃圾邮件列表中判断某邮箱是否垃圾邮箱（同理，垃圾短信）
- 缓存穿透，将所有可能存在的数据缓存放到布隆过滤器中，当黑客访问不存在的缓存时迅速返回，避免缓存及DB挂掉。
- 黑名单过滤。

## 20、如何缓解缓存击穿的问题

**产生原因一：缓存中没有，数据库中有。**一般是多是出现在数据初始化，以及key过期的情况。他的问题在于重新写入缓存需要一定的时间，如果是在高并发的情况下，过多的请求会打到DB上，给DB造成很大的压力。

解决方案：

- 设置热点的缓存永不过期。要注意永不过期数据一致性会有问题。所以要给value设置一个逻辑过期时间，然后后台再开一个线程，扫描这些key，定期刷新。

**产生原因二：某些非常热点的数据key 过期，大量的请求打到后端 。**

解决：

- 分布式锁的线程，从数据库拉数据（允许少数的线程取访问数据库并产生缓存），然后其他的线程等待。

- 后台任务针对过期的key 自动刷新。（设置随机的过期时间）

## 21、**如何缓解缓存雪崩的问题。**

**产生原因：大量的请求，或者大量的缓存key同时失效，大量的请求同时请求到数据库。**

解决：

- 多级缓存，不同级别的key设置不同的超时时间。

- 随机超时，key的超时时间随机设置，防止同时超时

- 在架构层解决，提升系统可用性，监控，报警完善。





## 22、如何保证Redis与数据库一致性

当我们对数据修改的时候，实现删除缓存还是先写入数据库。

**操作方式一：先删除缓存，在写数据**。

**产生的问题：**在高并发场景下，当第一个线程删除了缓存，还没有来得及写入数据库，第二个线程读取数据时会发现缓存为空，那么就会读取数据库的旧数据，读完之后又会将读取到的结果写入缓存，这样缓存中数据就是脏数据。

**解决：** **先操作缓存，但是不删除缓存，将缓存修改为一个特殊值，当客户端读取到这个特殊值时，休眠一会再去查Redis。**（需要注意的问题：①对业务是由侵入的。②：休眠时间对性能有影响）



**操作方式二：** **延时双删：先删除缓存，然后再写数据库，休眠一小会在删除缓存。**

**产生的问题：** 如果过写操作很频繁，同样会有脏数据的的问题。

解决：这种方式主要针对写操作不频繁的场景。



**操作方式三： ** **先写数据库，再删除缓存**

**产生的问题：** 如果数据写完了以后缓存修改失败。数据就会不一致。

**解决：**

- 给缓存设置过期时间。（在缓存时间内，数据不一致）
- 引入MQ 保证原子操作。（在MQ重试时间内数据不一致）

## 23、Redis 如何设置key的过期时间。它的键删除策略实现原理是什么

设置过期时间 

```python
expair key time 

setnx key value time
```



Redis 过期key删除机制有两种，一种是被动方式，一种是主动方式。

懒汉式式删除（被动）
含义：key过期的时候不删除，每次通过key获取值的时候去检查是否过期，若过期，则删除，返回null。

优点：删除操作只发生在通过key取值的时候发生，而且只删除当前key，所以对CPU时间的占用是比较少的，而且此时的删除是已经到了非做不可的地步(如果此时还不删除的话，我们就会获取到了已经过期的key了)


定期删除 （主动）
含义：每隔一段时间执行一次删除过期key操作

优点：通过平衡控制**执行效率**和**执行时长**，来减少删除操作对CPU时间的占用。

遍历每个database(默认16个)，检查当前库中指定个数的key(默认是20个)，随机抽查这些key，如果有过期的就删除。并且程序中有一个全局变量，用来记录扫描到了哪一个数据库（database）。



Redis 同时使用以上两种删除策略。

## 24、Redis的RDB和AOF机制

RDB（redis database）：在指定**时间间隔内**将内存中的**数据集快照**写入磁盘，也就是快照，它恢复是将快照文件直接读到内存里面。（在Redis 的配置文件内设置时间间隔）**RDB 默认开启。**

备份是如何执行的：
Redis 会单独的创建一个fork子进程来持久化，会先将数据写入一个临时文件中，等持久化过程都结束了，在用这个临时文件替换上次持久化的文件，整个过程主进程是不进行任何IO操作的，这就确保了极高的性能，如果需要大规模的数据的恢复，且对于数据的完整性要求不那么敏感，那么RDB的方式要比AOF的方式更加的额高效，RDB的缺点就是**最后一次持久化的数据可能会丢失。**


fork的作用就是复制一个与当前进程一样的进程，新的进程的所有的数据（变量，程序计数器等）都和原进程一样，但是是一个全新的进程，并作为原进程的子进程。


```shell
# 配置文件解释


# 检查数据完整性、默认值 yes 在存储跨照后让Redis 使用CRC64算法来对数据进行校验。但是这样做会损失大约10%的性能。推荐开启。
rdbchecksum  yes


# 当Redis 无法写入磁盘的时候，直接关掉Redis的写操作，推荐yes
stop-writes-on-bgsave-error yes

# Redis 压缩文件
rdbcompression yes


# 设置将数据写入磁盘的时间间隔， 默认 六十分钟1次、五分钟一百次、一分钟一万次
save 300 100
```




RDB的优势：

- 适合大规模数据的恢复。
- 对数据完整性和一致性要求不高时使用。
- 节省磁盘空间。
- 回恢复度快。


RDB的劣势：

- 在写入临时快照的时候，数据被克隆了一份，大致两倍的膨胀性需要考虑。
- 虽然Redis在fork时使用了写时拷贝技术，但是如果数据量庞大还是比较消耗性能。
- 在备份周期在一定时间间隔内做一次备份，所以如果Redis以外关掉，就会丢失最后一次快照的修改。

RDB的备份恢复：
默认Redis启动会自动将Redis的快照文件（dump.rdb）读取到内存中。手动恢复的话只需要将快照文件复制到Redis启动目录下。



AOF（Append Only File）：以日志的形式来记录每个**写操作**（增量保存），将Redis执行过的所有**写/修改/删除指令记录下来（读操作不记录）**，只许追加文件但是不可以改文件，Redis启动之初，会自动读取范围见重新构建数据，换言之Redis重启的话就会根据日志文件的内容将写指令重头到尾在执行一遍，以完成数据的恢复工作。**AOF默认不开启。**


开启AOF：

```shell
# 默认为no 不开启， 将其改为yes 开启。
appendonly no

```


**如果RDB和AOF同时开启，Redis 默认会读取AOF的配置文件来恢复数据。** 


AOF 异常修复：
如果遇到AOF文件损坏，通过 redis-check-aof   --fix  appendonly.aof 进行恢复。


AOF的同步频率设置：


```shell
# 设置AOF的同步频率 。
	always：始终同步。
	everysec ：每秒同步，每秒记入日志一次，如果宕机当前秒的数据可能会丢失。
	no：redis 不主动同步，把同步的时机交给操作系统。
appendfsync always   
```


Rewrite 压缩
AOF 采用文件追加方式，文件会越来越大，为避免出现此种情况，新增了重写机制，当AOF文件的大小超过设置的阈值时，Redis回启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集，可以使用命令bgrewriteaof。


重写的原理：
AOF文件持续增长而过大时， 会fork 出一条新的进程将文件重写（也是先写临时文件最后在rename）Redis4.0后的版本重写，实际上就是把RDB的快照，以二进制的形式附在新的aof的头部，作为已有的历史数据，替换掉原来的流水操作。


AOF持久化的流程：

- 客户端的请求命令会被append追加到AOF的缓冲区内。
- AOF缓冲区根据AOF持久化策略（always/everysec/no）将操作sync同步到磁盘中的.aof文件中去。
- AOF文件大小超过重写策略或者手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量。



### AOF与RDB对比：

- AOF文件比RDB大，且更安全，但恢复速度慢。
- AOF文件比RDB更新频率高，优先使用AOF还原数据。
- RDB性能比AOF好。
- 如果两个都加载了Redis默认优先加载AOF。

##  25、Redis 主从复制核心原理

主从复制配置文件

```
```



核心原理：通过执行slaveof 命令，让一个服务器复制另一个服务器的数据，主数据库负责写操作，从数据库负责读操作，当写操作导致数据变化时，会自动将数据同步给从数据库。

全量复制：

- 主节点通过bgsave命令fork子进程，进行RDB持久化（该过程是非常消耗CPU、内存的、硬盘IOd的）。
- 主节点通过网络将RDB文件发送给从节点，对从节点的带宽会有很大的消耗。
- 从节点清空老数据，载入新的RDB文件是阻塞的，无法响应客户端的命令，如果从节点执行bgrewriteof，也会带来额外的消耗。

增量复制：

- 复制偏移量：执行复制的双方主从节点分别会维护一个复制偏移量offset。
- 复制及压缓冲区：主节点内部维护一个固定长度的、先进先出的队列作为复制缓冲区，如果缓冲区超过最大长度，那么只能进行全量复制。
- 每个Redis启动都会产生一个ID， 主节点还会将自己的ID发送给从节点，如果主节点挂掉重新选举，主节点ID不一致那么只能全量复制。如果一致那就继续使用增量复制。

![](https://tc.chaizz.com/2e7fbffc46d411ec9d7c5254006b8f1d.png)



## 26、 索引回表 索引覆盖  索引下推



针对以上索引问题，首先要知道什么是主键索引、非主键索引、聚簇索引、非聚簇索引。

主键索引：即MySQL的索引，如果没有主键那么MySQl会自动在表中挑选一个唯一且非空的字段来当做主键索引，如果没有的话MySQL内部自己会创建一个ROW_ID来当做主键，也会建立主键索引。主键索引的叶子结点存储的是整行的数据。

非主键索引：即非主键以外的列建立的索引。非主键索引存储的是主键索引的值。

### 什么是索引回表？

索引回表指的就是在查询某一列数据是判断条件为非主键索引，name查到这条复合条件的所有记录就需要在根据非主键索引获得的主键索引的值，在取主键索引的B+树中在此查询一次才能获取到全部的数据。例如：

```mysql
# ID 是主键索引 ，只需要一次查询就可以获取符合条件的全部记录。
select * from ex_table where ID=1;
```



```mysql
# n 是非主键索引，查询到的结果是符合条件的主键索引的ID，所以还需要早根据主键的ID,再在主键索引的B+树上查询一次
select * from ex_table where n = 5;
```

以上情况就是索引回表。



### 什么是索引覆盖？

如果执行的语句是 :

```mysql
select idfrom T where n between 1 and 10;
```

现在的SQL只需要得到ID 的值，而 ID 的值已经在 n 索引的B+树上了，因此可以直接获得查询结果，不需要回表。也就是说，在这个查询里面，索引 n已经“覆盖了”我们的查询需求，我们称为覆盖索引。



### 什么是索引下推？

索引下推（index condition pushdown ）简称ICP，在MySQL5.6的版本上推出，用于优化查询，默认是开启的，可以通过以下的命令关闭：

```mysql
SET optimizer_switch = 'index_condition_pushdown=off';
```

当使用索引下推时如果存在某些被索引的列的判断条件时，MySQL服务器将这一部分判断条件传递给存储引擎，然后由存储引擎通过判断索引是否符合MySQL服务器传递的条件，只有当索引符合条件时才会将数据检索出来返回给MySQL服务器 。

索引下推的好处：

**索引条件下推优化可以减少存储引擎查询基础表的次数，也可以减少MySQL服务器从存储引擎接收数据的次数。**



假如有以下MySQL

```mysql
# 索引值为 name age 为组合索引。

select * from user where name like '张%' and age = 20;
```



在关闭索引下推的时候，InnoDB引擎会根据只name找到复合条件的索引字段，如下图中的左边绿色，然后就将数据返回给MySQL服务器，由MySQL服务器去判断其他的符合条件的数据。MySQL服务器会拿着查到的ID：1、2  在进行回表查询。

![](https://tc.chaizz.com/161026e0486011ec9d7c5254006b8f1d.png)





在使用索引下推的时候，InnoDB会直接找出符合索引条件的字段的ID，将符合条件的结果发送给MySQL服务器，这个过程只需要回表一次。





# 三、操作系统

## 1、进程和线程的区别

**CPU密集型计算（CPU-bound）：**Io可以再很短的时间结束 ，而需要CPU进行大量的计算。

比如： 压缩解压缩， 加密解密，正则的表达式搜索

**IO密集型计算(I/O-bound)：**指系统大部分时间是在等IO写入读取操作，CPU占用比较低。

比如：文件处理，网络爬虫操作，读写数据

**进程**：是对程序运行的封装，是操作系统调度资源的基本单位。进程切换消耗的资源比较大。效率比较低。

**线程**：是进程的基本单位，一个进程至少一个线程。可以实现进程的并发（并发是假的并行，相当于来回切换）。线程切换需要的资源一般，效率也一般。（在不考虑GIL的情况下）。

一个进程包括多个线程，线程依赖进程存在，共享进程的内存。

共享数据会导致线程安全。可以使用线程锁。或者在程序设计的时候就避免这种情况出现。

**为什么进程切换比线程切换消耗大：**

进程切换需要两步：①：切换页目录，使用新的地址空间。②：切换内核和硬件上下文。

对于Linux来说线程和进程最大的区别就是在于虚拟地址空间，每个进程都有自己的虚拟地址空间，而线程是共享进程的地址空间的，因此同一个进程中线程的切换不涉及到虚拟地址空间的转换。把虚拟地址转换为物理地址需要查找页表，页表查找是一个很慢的过程，因此通常使用TLB(Translation Lookaside Buffer)来缓存页地址，用来加速页表查找。当进程切换后页表也要进行切换，页表切换后TLB就失效了，那么虚拟地址转换为物理地址就会变慢，表现出来的就是程序运行会变慢，而线程切换则不会导致TLB失效，因为线程线程无需切换地址空间，因此我们通常说线程切换要比较进程切换快。

 

**保证线程安全的方式：**

- 互斥锁：通过互斥机制防止多个线程同时访问公共资源。

- 信号量：控制同一时刻多个线程访问资源的线程数。

- 事件（信号）：通过通知的方式实现。

**进程之间通信的方式：**

- 匿名管道（pipe）：管道是一种半双工的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常是指父子进程关系。

- 有名管道 (named pipe) ： 有名管道也是半双工的通信方式，但是它允许无亲缘关系进程间的通信。

- 信号：信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。

- 消息队列（Queue）：消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。
  - Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者在网站中的所有动作流数据。
  - RabbitMQ : 是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。

- 共享内存：共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。

- 信号量：信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。

- 套接字（socket） 用的比较多。套接字（socket）为通信的端点，每个套接字由一个 IP 地址和一个端口号组成。通过网络通信的每对进程需要使用一对套接字，即每个进程各有一个。
  - 服务进程：socket()->bind()->listen()->accept()->read()->write()->...->close()
  - 客户进程：socket()->connect()->write()->read()->...->close();

 

**线程之间的通信方式：**

共享变量、共享内存、共享数据库、消息队列

Python中如何使用对进程（在CPU密集型中使用）：

Multiprocessing ：多进程模块  

 

## 2、五种IO模型

阻塞式IO （Blocking IO）

非阻塞式IO （NonBlocking IO）

IO 多路复用 （IO Multiplexing）

信号驱动IO（Signal Driven IO）不常用

异步IO （Asynchronous IO）不常用

## 3、操作系统实现多路复用的方法 

IO多路复用的本质就是select/poll/epoll 去监听多个socket对象，如果其中的socket对象有变化，只要有变化，用户进程就知道了。

Select 是不断的轮询监听的socket， socket的个数有限制，一般为1024个。

Poll 还是采用轮询的方式监听，之不过没有个数的限制。

Epoll 并不是采用轮训的方式去监听，而是当socket有变化的时候通过回调的方式主动地告知用户进程。

表面上看epoll的性能最好，但是在连接数少，连接数十分活跃的情况下，selecthe poll性能会比epoll好。因为epoll的通知机制需要很多回调函数。

## 4、Python 实现IO多路复用的方法：

也是基于操作系统的select ,poll epoll方法。Pyhon3 中实现了selectors 模块。

事件类型：EVENT_READ 、EVENT_WRITE 

DefaultSelector：自动根据系统来选择IO模型。其中的一些方法

a)   register（fileobj events data = none）

b)  unregister（fileobj）

c)   Modifiy(fileobj events data = none)

d)  Select(timeout=none, returns[keys, events])

e)   Close()

## 5、Python的并发网络库：

Gevent 、asyncio、tornado

Tornado：是一个并发网络编程库，也是一个Web框架。

Gevent：基于几个绿色线程实现并发。底层是基于c语言实现的。基于 monkey patch gevent修改了内置的socket 改为非阻塞。 经常配合gunnicorn 部署作为wsgi server 。 

Asyncio ：基于原生的协程实现的。

 

## 6、Linux中有哪些调度算法？

先来先服务调度算法和短作业优先调度算法

 

## 7、操作系统如何管理内存的？

内存又分为虚拟内存和物理内存。

虚拟内存的基本思想就是每个进程都有独立的逻辑地址空间，内存被分为大小相等的块，称为页，每个页都是一段连续的地址，对于进程来看貌似有很多的内存空间，但切实只有一部分是物理内存地址。

缺页中断，

## 8、LRU:最近最少使用(Least Recently Used)算法

基于最近使用的也慢数据在未来一段时间仍有很大可能被使用，已经很久没有使用的数据在未来的很长一段时间内也仍然不会被使用这种思想的一种淘汰机制，它的主要衡量指标是时间，第二衡量指标是次数。利用双向链表来实现。

## 9、LFU（Least Frequently Used）算法

即最少访问算法，根据访问缓存的历史频率来淘汰数据，核心思想是“如果数据在过去一段时间被访问的次数很少，那么将来被访问的概率也会很低”。

数据结构： 一般会维护两个数据结构：

哈希：用来提供对外部的访问，查询效率更高；

双向链表或队列：维护了对元素访问次数的排序

**优点：**

一般情况下，LFU效率要优于LRU，能够避免周期性或者偶发性的操作导致缓存命中率下降的问题

**缺点：**

复杂度较高：需要额外维护一个队列或双向链表，复杂度较高

对新缓存不友好：新加入的缓存容易被清理掉，即使可能会被经常访问

缓存污染：一旦缓存的访问模式发生变化，访问记录的历史存量，会导致缓存污染；

内存开销：需要对每一项缓存数据维护一个访问次数，内存成本较大；

处理器开销：需要对访问次数排序，会增加一定的处理器开销



# 四、网络



## 1、Python 底层的网络编程模块有哪些？

```python
Socket 、urllib、requests、grab、pycurl
```



## 2、简述OSI七层含义

\-   应用层：HTTP、FTP、NFS

\-   表示层：Telnet、SNMP

\-   会话层：SMTP、DNS

\-   传输层：TCP、UDP

\-   网络层：IP、TCP、ARP

\-   数据链路层：Etherent、PPP、PDN、SLIP、FDDI

\-   物理层：TEEE 802.1A、TEEE 802.11

应用层、表示层、会话层 也可以称之为应用层。

## 3、输入一个URL中间的过程：

- 首先首先浏览器会解析域名找到对应的IP地址。
  - 浏览器会首先搜索浏览器自身的DNS缓存，(缓存时间较短，大概只有一分钟，且只能容纳1000条缓存)，看自身缓存是否有该域名对应的条目，而且没有过期，如果有且没有过期解析到此此结束。
  - 如果浏览器自身的缓存李，没有找到对应的条目，那么浏览器就会搜索系统自身的DNS缓存，如果找到且没有过期，则停止搜索，解析结束。
  - 如果在系统的DNS缓存也没有找到，那么尝试读取hosts文件，看看这里面是否有对应的IP地址，如果有则解析成功。
  - 如果hosts文件也没有，浏览器就是发起一个DNS系统的调用，就会向本地配置的首选DNS服务器(一般是电信运营商提供的)，发起域名解析请求（通过UDP协议向DNS的53端口发期请求，这个请求是递归请求，也就是运行商必须提供给我们该域名的IP地址），运行商的DNS服务器首先查找自身的缓存，找到对应的条目且没有过期，则解析成功，如果没有，则由运营商的DNS代我们我们的浏览器发送迭代DNS解析,首先会找根域的DNS的I地址（这个DNS服务器都内置13台根域的DNS的IP地址），然后进一步请求，一般四部就可以找到域名对应的IP地址的。
- 浏览器调用socket 函数，发起TCP 请求 （三次握手）与服务器建立连接。
- 建立连接之后发起应用层http的请求，如果有代理的话 到达nginx 然后再到uwsgi 最后再到web应用响应。
- 浏览器得到并解析解析html代码。
- 然后web应用在执行他的逻辑，然后返回response，通过tcp返回给用户，最后就会执行TCP的四次挥手。

## 4、TCP的三次握手四次分手：

**第一次握手：**

客户端将TCP报文标志位SYN置为1，随机产生一个序号值seq=J，保存在TCP首部的序列号(Sequence Number)字段里，指明客户端打算连接的服务器的端口，并将该数据包发送给服务器端，发送完毕后，客户端进入SYN_SENT状态，等待服务器端确认。

**第二次握手：**

服务器端收到数据包后由标志位SYN=1知道客户端请求建立连接，服务器端将TCP报文标志位SYN和ACK都置为1，ack=J+1，随机产生一个序号值seq=K，并将该数据包发送给客户端以确认连接请求，服务器端进入SYN_RCVD（同步已发送状态）状态。

**第三次握手：**

客户端收到确认后，检查ack是否为J+1，ACK是否为1，如果正确则将标志位ACK置为1，ack=K+1，并将该数据包发送给服务器端，服务器端检查ack是否为K+1，ACK是否为1，如果正确则连接建立成功，客户端和服务器端进入ESTABLISHED（已建立连接）状态，完成三次握手，随后客户端与服务器端之间可以开始传输数据了。

三次握手：
 “喂，你听得到吗？”
 “我听得到呀，你听得到我吗？”
 “我能听到你，今天 balabala……”

**TCP的四次挥手**：

 **第一次分手：**

Client端发起挥手请求，向Server端发送标志位是FIN报文段，设置序列号seq，此时，Client端进入FIN_WAIT_1状态，这表示Client端没有数据要发送给Server端了。

**第二次分手：**

Server端收到了Client端发送的FIN报文段，向Client端返回一个标志位是ACK的报文段，ack设为seq加1，Client端进入FIN_WAIT_2状态，Server端告诉Client端，我确认并同意你的关闭请求。

**第三次分手：**

Server端向Client端发送标志位是FIN的报文段，请求关闭连接，同时Client端进入LAST_ACK状态。

**第四次分手 ：**

Client端收到Server端发送的FIN报文段，向Server端发送标志位是ACK的报文段，然后Client端进入TIME_WAIT状态。Server端收到Client端的ACK报文段以后，就关闭连接。此时，Client端等待2MSL的时间后依然没有收到回复，则证明Server端已正常关闭，那好，Client端也可以关闭连接了。

 

## 5、为什么TCP需要四次分手呢？

因为建立连接时ACK和SYN可以放在一个报文里面来发送，而关闭连接时，被动的关闭方，可能还需要发送一些数据，再发送DIN报文确认同意可以关闭连接了，所以这里的ACK和Fin报文是分开发送的。

## 6、TCP/UDP的区别：

**相同点：**

UDP协议和TCP协议都是传输层协议。

**不同点：**

\-   TCP：面向有连接的（建立之前必选建立连接，结束之后关闭连接），可靠的、 基于字节流的。

\-   UDP：无连接的（知道对端的IP和端口号就直接进行传输, 不需要建立连接。）不可靠（没有确认机制, 没有重传机制; 如果因为网络故障该段无法发到对方, UDP协议层也不会给应用层返回任何错误信息。）。面向报文的（应用层交给UDP多长的报文, UDP原样发送, 既不会拆分, 也不会合并。）。

TCP 只支持点对点的，UD支持一对一，一对多，多对多的交互通信。

## 7、UDP如何尽量保持可靠

UDP他不属于连接性协议，因为具有资源消耗小，处理速度快的优点，随意即时通许，音视频数据在传输是使用UDP比较多，因为他们偶尔丢一两个包对数据也不会造成太大的影响，传输层无法保证数据的可靠传输，只能在应用层来实现，实现的方式可以参考TCP的可靠性传输的方式，只是实现不在传输层，实现转移到了应用层。

关键在于两点：

- 提供超时重传，能避免数据丢失
- 提供确认序列号，可以对报文进行确认和排序。



## 8、TCP的keepalive和 http 的keep alive的区别

http的keep-alive是为了让TCP活的更久一点，一边在同一个连接上传送多个http，提供socket效率，而TCP的keep-alicve是TCP的一种检测TCP连接的保鲜机制。检查TCP连接是否活跃。

## 9、HTTP的1.0 、1.1、2.0 的主要区别

**HTTP1.0：**

- 无状态，无连接的

**HTTP1.1:**

- 支持长连接，请求管道化，请求并行发送，响应仍然有序返回
- 增加缓存的处理，新的字段：cache-control
- 增加Host字段，适应虚机主机技术发展，即一台服务器支持多台主机。
- 支持断点传输

**HTTP2.0**:

- 二进制分帧
- 多路复用（连接共享）
- 头部压缩（encoder）
- 服务器推送

## 10、HTTP请求的组成

状态行：请求码、请求方法（method）、路径、版本

请求头：HOST，数据类型（content_type），数据长度(content_length)，接收编码（accept-encoding）连接（keep-alive）、user-agent

请求体内容

## 11、HTTP响应的组成：

状态行：响应码、请求方法（method）、路径、版本

响应头：缓存（cache-contral）、数据类型（content_type），数据长度(content_length)、连接（cinnection）、

响应正文：内容

 

## 12、HTTP响应的状态码：

1xx：服务器已经收到 需要请求者继续操作

2xx：成功，操作成功并接受处理

3xx：重定向 （301：用久重定向，302：临时重定向，304：not modified）

4xx：客户端错误（401：未认证，403：forbidben 没权限，404：not found 405：请求不被允许）

5xx：服务端错误；（500：服务错误，502：对用户访问请求超时）

## 13、HTTP请求方法

GET/POST/PUT/DELETE

## 14、GET/POST的区别：

GET的请求参数是放在URL上面的，是明文的（长度是有限的）。POST是放在请求体里面的相对更安全。

## 15、HTTP长连接：

短连接：建立连接，数据传输，关闭连接，（连接建立和关闭的开销大）。

长连接：Connection:keep-alive, 来保持TCP不断开。

## 16、Cookie 和 Session 的区别：

Session 是服务器生成之后返回给客户端，通过URL或者是cookie

Cookie 是实现session的一种机制，通过http cookie 字段实现。

Session 是通过服务器保存session识别用户，cookie保存在客户端。



## 17、 网络TCP socket 编程实现原理

服务进程：socket()->bind()->listen()->accept()->read()->write()->...->close()

客户进程：socket()->connect()->write()->read()->...->close();

**创建客户端：**

1. 首先创建sokect对象

2. 创建连接 connection 

**创建服务端**

1. 创建socket 对象。

2. 绑定本地地址

3. Socket对象监听

**使用socket 发送http请求：**

1. 创建socket 对象。

2. 连接http 地址。

3. 发送http请求。

4. 接收返回对像。

5. 关闭请求连接。

# 五、Python WSGI 与WEB框架的常考问题

## 1、什么是WSGI：和web框架交互的一个规范。

WSGI全称：Web Server Gateway Interface，是一个网关服务接口协议。

主要是解决python web server 乱象 mod_python 、CGI、FastCGI 等。描述了 web server (Gunicorn/Uwsgi) 如何与web框架（django/flask）交互，web 框架如何处理请求。

实现了协议的有Flask使用的werkzurg/ django常用的uwsgi（性能好一点） 和 gunicorn 和 Python 自带的 wsgiref （一种wsgi参考）。

## 2、django请求的生命周期？

​    

用户在浏览器输入URL的时候，浏览器会生成请求头，和请求体发送给服务端。请求头he请求体中会包括浏览器的动作（这个动作通常为GET或者POST 体现在URL中）。

URL经过Django的服务器uwsgi,（如果有代理服务器的话，先经过代理服务器在由代理服务器设置的代理，转到uwsgi）然后请求到达django项目的中间件。通过中间件以后在到达路列表，进行匹配，匹配到路由规则，在访问视图层。

视图函数根据客户端的请求查询相应的数据，返回给django，然后django在把获取的序列化的数据返回。

## 3、列举django中间件的五个方法

自定义中间件需要继承父类 （MiddlewareMixin）。

**process_request(request) ：**此方法是在视图函数执行之前执行的。该方法包含一个参数 request。这个request和视图函数中的request是一样的。返回值可以是NONE 也可以是HttpeRsponse ，如果是none 那就走下一个中间件，如果是HttpeRsponse，django 将不会再走后面的视图函数，那就直接已改中间件为起点，倒序执行中间件，且执行的是视图函数执行之后的方法。该方法的应用场景：可以做认证、权限先关的事情。

**process_resopnse（request，response）：**此方法是在视图函数执行之后执行的。该方法 有两个参数，request是请求对象，response是视图函数返回的httpresponse。该方法必须要有返回值，而且必须是response。

**process_exception(request, exception) ：**此方法有两个函数，request 是HttpRequest 对象，exception 是视图函数异常产生的 Exception 对象。该方法只有在视图函数中出现异常了才执行，按照 settings 的注册倒序执行。在视图函数之后，在 process_response 方法之前执行。方法的返回值可以是一个 None 也可以是一个 HttpResponse 对象。如果是NONE 则会直接返回一个500的状态码，不会往下执行视图函数，如果是返回HttpResponse ，页面不会报错没返回状态码200，视图函数不执行，该中间件后续的 process_exception 方法也不执行，直接从最后一个中间件的 process_response 方法倒序开始执行。

 **process_view(request,view_func, view_args, view_kwargs) ：**此方法是在视图函数之前，process_request 方法之后执行的。该方法有四个参数request 是 HttpRequest 对象；view_func 是 Django 即将使用的视图函数；view_args 是将传递给视图的位置参数的列表；view_kwargs 是将传递给视图的关键字参数的字典。返回值可以是 None、view_func(request) 或 HttpResponse 对象

**process_template_responseprocess（）** ：这个渲染模板的时候执行的。

## 4、简述什么叫FBV和CBV

FBV 叫做基于函数的视图，CBV是基于类的视图，使用CBV的优点，提高代码的复用性，使用面向对象的技术，可以用不同的函数（get()/post()）针对不同的请求处理，而不是通过if 判断请求方式。

## 5、Django的内置组件

admin 是对modle 中的数据记性可视化的增删改查的组件。

model 数据库数据结构的映射对象。

form 生成html的代码，对数据进行检验，校验数据并返回。

## 6、django的request对象是在什么时候创建的？

```python
class WSGIHandler(base.BaseHandler):

    request = self.request_class(environ)
```

请求走到WSGIHandler类的时候，执行cell方法，将environ封装成了request

## 7、如何给CBV的程序添加装饰器。

可以使用method_decorator 在类的get/post方法上添加 例如：@ method_decorator（func）

给类添加在类名上 例如：@ method_decorator（func，name=post）

## 8、列举django ORM中方法

all():         查询所有结果 

filter(**kwargs):    它包含了与所给筛选条件相匹配的对象。获取不到返回None

get(**kwargs):    返回与所给筛选条件相匹配的对象，返回结果有且只有一个。 如果符合筛选条件的对象超过一个或者没有都会抛出错误。

exclude(**kwargs):   它包含了与所给筛选条件不匹配的对象

order_by(*field):    对查询结果排序

reverse():       对查询结果反向排序 

count():        返回数据库中匹配查询(QuerySet)的对象数量。 

first():        返回第一条记录 

last():        返回最后一条记录 

exists():       如果QuerySet包含数据，就返回True，否则返回False

values(*field):    返回一个ValueQuerySet——一个特殊的QuerySet，运行后得到的并不是一系 model的实例化对象，而是一个可迭代的字典序列。

values_list(*field):  它与values()非常相似，它返回的是一个元组序列，values返回的是一个字典序列

distinct():      从返回结果中剔除重复纪录

## 9、select_related和prefetch_related的区别？

前提：有外键存在时，可以很好的减少数据库请求的次数,提高性能

select_related通过多表join关联查询,一次性获得所有数据,只执行一次SQL查询。（针对于 一对一和一对多）

prefetch_related分别查询每个表,然后根据它们之间的关系进行处理,执行两次查询。（针对多对多）

select_related方法执行一次数据库查询，prefetch_related方法执行两次数据库查询

## 10、列举django ORM 中三张能写sql语句的方法。

使用execute(sql语句) 类似pymysql的形式。

使用extra() 方法 quersyt.extra(select={“key” ：“原生SQL语句”})

使用raw()方法  对象.objects.raw(sql语句)

## 11、values和values_list的区别？

values : queryset类型的列表中是字典

values_list : queryset类型的列表中是元组

## 12、Django的queryset有哪些特性？

主要有两个特性，一个是惰性的，另一个是的自带缓存。

惰性：在使用查询语句的时候，Django不会去主动的查询数据库，只有你使用了查询的对象，Django才会去访问数据库。

缓存：第一次访问数据库以后Django会把得到的数据保存在queryset内置的cache中。Django就不需要在进行重复的查询了。

## 13、Django的模型继承有哪几种方式? 它们有什么区别以及何时使用它们?

Django的模型继承有如下三种：

抽象模型（avstract model）：Django不会为抽象模型在数据库中生成自己的表，父类Meta 中的abstract=True 也不会传递给子类，如果被你发现多模型有很多共同字段的时，需要用抽象模型继承。

多表模型继承（multi-table-inheritance）：多表模型继承与抽象模型继承最大的区别在于Django也会为父类模型建立自己的数据库表。同时隐士的在父类和子类之间建立一个一对一关系。

代理模型（proxy model）：如果我们只改变某个模型的行为方法，而不是添加额外的字段或者创建额外的数据表，我们就可以使用代理模型，设置一个代理模型需要子类模型meta选项中设置proxy=True，django不会为代理模型生成新的数据表。

## 14、简单说说看 Django的CSRF防御机制。

Django的CSRF是通过Django的中间件 （django.middleware.csrf.CsrfViewMiddleware）来实现的。主要流程如下：

Django相应来自客户端的请求的时候，会在服务器生成一个csrftoken（一串64位的随机码），把这个token放在请求头的cookie里发给客户端返回给用户。

所有通过POSt提交的额表单，都要携带一个隐藏字段，通过模板文件中的`{%csrf_token%}`标签生成。

当用户通过提教交POST的时候，django会从请求头cookie中获取这个token的值，与生成的只比较是否一致。

## 16、如何从数据表中获取一个随机的对象。

可以使用order_by（”?”）.first()

## 17、说说aggregate和annotate方法的作用。

Aggregate 是聚合的意思，是对一组值(比如queryset的某个字段)进行统计计算，并以字典(Dict)格式返回统计计算结果。支持 count（）max(),min()sun()avg()。

Annotate ：可以理解为分组，对数据集先进行分组然后再进行某些聚合操作或排序时，需要使用annotate方法来实现。与aggregate方法不同的是，annotate方法返回结果的不仅仅是含有统计结果的一个字典，而是包含有新增统计字段的查询集(queryset）

## 18、常用的框架Django/Flask/Fastapi的对比。 

Django ：大而全，内置ORM、Admin, 第三方的插件比较多。

Flask ：微框架、插件机制，比较灵活。

Tornado ：支持异步的微框架，和异步网络库。

## 19、Django 常用的第三方插件：

django-taggit 可以在文章中当作标签使用

django- Celery 异步分布式的队列。

djangorestframework REST API 的框架。可以做 序列化，分页。权限的管理，认证机制。

django-cors-headers ：管理跨域操作的插件。

django-haystack 全文检索引擎 

django -simple 后台UI界面 

django -captcha Django的验证码

django-debug-toolbar debug 调试工具

## 20、Django中如何使用redis作为缓存

安装django-redis ，在settings 设置缓存的服务期设置。

```python
CACHES = {
  'default': {
    'BACKEND': 'django_redis.cache.RedisCache',
    'LOCATION': 'redis://your_host_ip:6379',
    "OPTIONS": {
      "CLIENT_CLASS": "django_redis.client.DefaultClient",
       "PASSWORD": "yourpassword",
    		},
  		},
	}
```



也可是设置超时时间 ：REDIS_TIMEOUT=7*24*60*60

## 21、如何在模板中获取当前访问url地址

| Method                     | output                                      |
| -------------------------- | ------------------------------------------- |
| request.path               | /search/                                    |
| request.get_full_path      | search/?keyword=django                      |
| request.build_absolute_uri | https://jackeygao.io/search/?keyword=django |

## 22、Django信号(Signals)的工作原理, 主要应用场景及内置信号。

Django 提供一个了“信号分发器”机制，允许解耦的应用在框架的其它地方发生操作时会被通知到。通俗而讲Django信号的工作原理就是当某个事件发生的时候会发出一个信号(signals), 而监听这个信号的函数(receivers)就会立即执行。应用场景有很多比如：创建用户的时候在创建一个一对一关系的用户信息的模型对象。或者是用户下订单的时候邮件通知管理员的情况。

Django的内置信号 ：

```python
django.db.models.signals.pre_save & post_save在数据模型调用 save()方法之前或之后发送。

django.db.models.signals.pre_init& post_init在模型调用_init_方法之前或之后发送。

django.db.models.signals.pre_delete & post_delete在模型调用delete()方法或查询集调用delete() 方法之前或之后发送。

django.db.models.signals.m2m_changed在模型多对多关系改变后发送。

django.core.signals.request_started & request_finished Django建立或关闭HTTP 请求时发送。
```



## 23、什么情况下需要自定义context_processors(上下文处理器)

当你需要一个视图函数或者模板提供或设置全局变量的时候，需要用上下文管理器，我们在试图和模板中随意使用request这个变量，就是因为django.core.contenxt_process_request 把request变成了一个全局变量。上下文管理器（content_process）很多地方都有用，例如：一些博客的标签、归档，这些公共的信息，是每个文章都会用的东西，如果存在数据库中，就会每次使用都要从数据库查询回去数据，造成资源浪费，如果通过context_process 设置为全局变量，就不需要再每次都要查询数据库了。

## 24、Django如何实现高并发？

使用NGINX进行反向代理。

数据库的分库和读写分离(主从复制)。

使用redis做缓存。

耗时任务(收发邮件/写入文件)使用celery 异步处理。

使用Gzip压缩静态文件。

使用CDN加速静态文件。

## 25、什么是MVC 

Modle：数据层，数据业务对象和数据库的交互(ORM)。

View：视图层，负责与用户交互和展示。 

Controller ：接收请求参数,调佣模型和视图完成请求。

## 26、什么是ORM

对象关系映射，用于实现业务对象与数据库表中字段映射。类似的有sqlalchemy 、Django 的 ORm 还有 Peewee

好处：代码更加的面向对象，代码量更少，灵活性更高，提升代码的开发效率。

## 27、WEB安全的问题

什么是SQL 注入：

通过特殊的参数传入web应用，导致后端执行了恶意代码。在动态的拼接SQL的时候产生。

如何防范：

永远不要相信用户的人任何输入

对输入的参数最好检查，过滤和转义特殊字符。

不要直接拼接sql使用ORM可以大大降低sql注入的风险。

数据库层：做好明文管理，不要存储明文敏感信息。

## 28、什么是XSS 

恶意用户将代码植入到提供给其他用户使用的页面中。未经转义的恶意代码输出到其他的用户浏览器被执行。

嵌入到页面中的js脚本被执行，攻击用户。

## 29、什么是前后端？有哪些优势。

后端只负责提供数据，不再渲染模板。前端获取接口实现。

好处：前后端解耦，接口复用，减少开发量。提升工作效率。更利于调试，测试和部署。

缺点：动态加载不利于SEO。

## 30、什么是 RESTful

是一种以资源为中心的WEB软件架构风格，可以用ajax 和restful web服务架构应用。

设计概念和准则：

所有的事物抽象为资源，资源对应唯一的标识。

资源通过接口进行操作实现状态转移。操作本身是无状态的。



## 32、Django和Flask有什么区别？

1、项目大小：

- Flask 小而美， 更加的灵活，根据业务有选择的使用第三方组件。进行定制化开发。但是同时也带来风险， 后期维护需要依赖第三方组件。
- Django 大而全，一站式开发，包含丰富的内部组件：中间件、admin、缓存、等等。

2、请求传递方式：

- Flask 在上下文管理器
- Django 通过请求传递

3、会话：

Flask 的session是以加密的方式保存到浏览器 cookie 

Django的session是保存到数据库。



## 32、Django的app和Flask的蓝图有什么区别？

项目结构：

- Django的app包含整应用的router、views、templates

- Flask的蓝图最小只包括路由和views。

注册方式：

- Flask 使用app.register_blueprint
- Django是在settings中注册

但是等到项目结构增大时，flask的蓝图趋近于Django的app。所以他们本质上没什么区别，都是为了内部的应用区分。



 

# 七 其他

## 1、编程语言的区别

- c/c++ 代码执行效率高， 很多语言的底层实现都是c，对代码要求比较高，需要自己实现一些低等的功能，内存管理等。

- java 企业级web开发语言。

- c# 微软开发的语言。
- javascript 一种运行在浏览器中的解释型的编程语言。
- Python 解释性语言，简洁，入门简单。封装性高，使用很少的语言，完成一些功能。
- Golang 语法和c接近，对并发性有比较好的支持，支持原生协程，运维云原生的主流语言。docker、k8s。



## 2、构造函数析构函数

构造函数：用于创建对象的函数。

析构函数：用于销毁对象的函数。

```python
class Foo():
    def __init__(**args, **kwargs): # 构造函数
        pass
    
    def __del__(): # 析构函数
        pass
```

