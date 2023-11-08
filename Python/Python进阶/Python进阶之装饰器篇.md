---
title: Python进阶之装饰器篇
author: chaizz
date: 2023-04-6
tags:
  - 开发环境配置
categories:
  - 开发环境配置
  - 系统环境
photos: ["https://origin.chaizz.com/python_18894.png"]
cover: "https://origin.chaizz.com/python_18894.png"

---

​    

<!--more-->

# Python进阶之装饰器篇



装饰器的概念：在函数**执行过程中**对函数添加额外的功能的方式，称为装饰器。我们只是将对函数添加功能的位置转移到了函数定义的位置。

在Python中一切皆对象，我们可以将一个函数当做另一个函数的参数， 亦可以将函数赋值给一个变量。



## 1. 普通装饰器

```python
import functools
import random
import time

def timer(func):
    @functools.wraps(func)
    def inner(*args, **kwargs):
        start_time = time.perf_counter()
        ret = func(*args, **kwargs)
        print(f'该函数：{func.__name__}耗时：{time.perf_counter() - start_time: .2f}s')
        return ret

    return inner


@timer
def random_sleep():
    time.sleep(random.random())


if __name__ == '__main__':
    random_sleep() # 该函数：random_sleep耗时： 0.12s
```



## 2. 带参数的装饰器

在原有的装饰器的基础上再嵌套一层。

```python
import functools
import random
import time


def timer(print_args=False):
    def warpper(func):
        @functools.wraps(func)
        def inner(*args, **kwargs):
            start_time = time.perf_counter()
            ret = func(*args, **kwargs)
            if print_args:
                print(
                    f'该函数：{func.__name__} args: {args}，kwargs: {kwargs} 耗时：{time.perf_counter() - start_time: .2f}s')
            return ret

        return inner

    return warpper


@timer(print_args=True)
def random_sleep():
    time.sleep(random.random())


if __name__ == '__main__':
    random_sleep() # 该函数：random_sleep args: ()，kwargs: {} 耗时： 0.77s
```



## 3. 可选参数装饰器

上述的带参数的装饰器，当我们设置了默认参数时，装饰器也需要使用`@timer()`的方式调用。我们可以使用仅限关键字参数的方法来实现，有默认参数时，不用添加括号也可以运行的装饰器。

```python
import functools
import random
import time


def timer(func=None, *, print_args=True):
    def warpper(_func):
        @functools.wraps(_func)
        def inner(*args, **kwargs):
            start_time = time.perf_counter()
            ret = _func(*args, **kwargs)
            if print_args:
                print(
                    f'该函数：{_func.__name__} args: {args}，kwargs: {kwargs} 耗时：{time.perf_counter() - start_time: .2f}s')
            return ret

        return inner
    if func is None:
        return warpper
    else:
        return warpper(func)


@timer()
def random_sleep():
    time.sleep(random.random())


if __name__ == '__main__':
    random_sleep() # 该函数：random_sleep args: ()，kwargs: {} 耗时： 0.49s
```

## 4. 类实现装饰器 -（函数替换）

```python
import functools
import random
import time


class Timer:

    def __init__(self, print_args=True):
        self.print_args = print_args

    def __call__(self, func):
        @functools.wraps(func)
        def inner(*args, **kwargs):
            start_time = time.perf_counter()
            ret = func(*args, **kwargs)
            if self.print_args:
                print(
                    f'该函数：{func.__name__} args: {args}，kwargs: {kwargs} 耗时：{time.perf_counter() - start_time: .2f}s')
            return ret

        return inner


@Timer()
def random_sleep():
    time.sleep(random.random())


if __name__ == '__main__':
    random_sleep() # 该函数：random_sleep args: ()，kwargs: {} 耗时： 0.52s
```



## 5. 类实现装饰器 -（实例替换）- （无参数）

```python
import random
import time


class Timer:

    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        start_time = time.perf_counter()
        ret = self.func(*args, **kwargs)
        print(
            f'该函数：{self.func.__name__} args: {args}，kwargs: {kwargs} 耗时：{time.perf_counter() - start_time: .2f}s')
        return ret


@Timer
def random_sleep():
    time.sleep(random.random())


if __name__ == '__main__':
    random_sleep()
```



## 6. 类实现装饰器 -（实例替换）- （有参数）

```python
import functools
import random
import time


class Timer:

    def __init__(self, func=None, *, print_args=False):
        self.func = func
        self.print_args = print_args

    def __call__(self, *args, **kwargs):
        start_time = time.perf_counter()
        ret = self.func(*args, **kwargs)
        if self.print_args:
            print(
                f'该函数：{self.func.__name__} args: {args}，kwargs: {kwargs} 耗时：{time.perf_counter() - start_time: .2f}s')
        return ret


def cost_time(**kwargs):
    return functools.partial(Timer, **kwargs)


@cost_time(print_args=True)
def random_sleep():
    time.sleep(random.random())


if __name__ == '__main__':
    random_sleep()
```



## 7. 使用wrapt创建装饰器 - 无参数



```python
import random
import time

import wrapt


@wrapt.decorator
def timer(wrapped, instance, args, kwargs):
    start_time = time.perf_counter()
    ret = wrapped(*args, **kwargs)
    print(f'该函数：{wrapped.__name__}耗时：{time.perf_counter() - start_time: .2f}s')
    return ret


@timer
def random_sleep():
    time.sleep(random.random())


if __name__ == '__main__':
    random_sleep()
```



## 8. 使用wrapt创建装饰器 - 有参数

```python
import random
import time

import wrapt

def timer(*, print_args=False):
    @wrapt.decorator
    def wrapper(wrapped, instance, args, kwargs):
        start_time = time.perf_counter()
        ret = wrapped(*args, **kwargs)
        if print_args:
            print(f'该函数：{wrapped.__name__}耗时：{time.perf_counter() - start_time: .2f}s')
        return ret
    return wrapper

@timer(print_args=False)
def random_sleep():
    time.sleep(random.random())


if __name__ == '__main__':
    random_sleep()
```





## 9. 可选参数的装饰器

```python
import functools
import random
import time

import wrapt


def timer(wrapped=None, print_args=False):
    if wrapped is None:
        return functools.partial(timer, print_args=print_args)

    @wrapt.decorator
    def wrapper(wrapped, instance, args, kwargs):
        start_time = time.perf_counter()
        ret = wrapped(*args, **kwargs)
        if print_args:
            print(f'该函数：{wrapped.__name__}耗时：{time.perf_counter() - start_time: .2f}s')
        return ret

    return wrapper(wrapped)


@timer(print_args=True)
def random_sleep():
    time.sleep(random.random())


@timer
def random_sleep2():
    time.sleep(random.random())


if __name__ == '__main__':
    random_sleep()
    random_sleep2()
```

