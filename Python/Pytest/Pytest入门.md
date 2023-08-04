---
title: Pytest入门
author: chaizz
date: 2023-7-29
tags:
  - pytest
categories:
  - pytest
photos: ["https://origin.chaizz.com/pytest_logo_curves.svg"]
cover: "https://origin.chaizz.com/pytest_logo_curves.svg"
---

​    

<!--more-->



> Pytest 是一个基于 Python 的测试框架，它提供了一种简洁优雅的方式来编写测试代码。在软件开发中，测试是非常重要的环节，它有助于验证代码的正确性、稳定性和可靠性。Pytest 框架的出现使得编写和执行测试变得更加简单和高效。
>
> 
>
> 为什么我们需要测试？在软件开发过程中，我们经常会遇到各种各样的需求变更、bug修复、功能扩展等情况，这些变动可能会对现有的代码产生意想不到的影响。而如果没有足够的测试覆盖，这些变动可能会导致代码的错误、功能失效或者性能下降。测试的作用就在于通过运行一系列的测试用例来验证代码的正确性，确保它能够按照预期的方式工作。测试能够帮助我们发现和解决问题，提高代码的质量和可维护性。
>
> 
>
> Pytest 框架相比其他测试框架有以下优势：
>
> 1. 简洁优雅：Pytest 使用简洁的语法和强大的断言库，使得编写测试代码更加易读、易维护。相比其他测试框架，Pytest 的测试用例更简洁、更清晰。
> 2. 自动化发现测试：Pytest 可以自动发现项目中的测试函数和测试类，无需手动编写测试套件。只需要按照规定的命名规则来命名测试文件和测试函数，Pytest 就能自动识别并执行。
> 3. 丰富的插件生态系统：Pytest 提供了丰富的插件机制，可以通过安装插件来扩展测试框架的功能。这些插件可以提供各种功能，如生成测试报告、代码覆盖率检查、性能测试等。
> 4. 强大的断言库：Pytest 提供了易于使用的断言库，可以快速编写断言语句来验证代码的预期行为。断言库具有灵活的表达能力，可以进行多种类型的断言，包括比较值、判断异常等。
>
> 总之，Pytest 是一个功能强大、易用且灵活的测试框架，能够帮助开发人员编写高质量的测试代码，提高软件的质量和可靠性。无论是单元测试、集成测试还是端到端测试，Pytest 都能够满足各种测试需求，并且减少测试代码的编写和维护工作量。



*以上序使用ChatGPT输出*。



## 1 Pytest 测试用例的默认规则

- 模块名必须以`test_`开头或者`_test`结尾。
- 方法或者函数名必须以`test_`开头。
- 类名必须以`Test`开头。
- 测试类中不能有`__init__`方法。
- 不建议在类中设置一些类示例方法。



```python
import pytest


def add_func():
    return 1 + 1


def sum_func():
    return sum([1, 2, 3, 4, 5])


def test_add():
    assert add_func() == 2


class TestCase:

    def test_sum(self):
        assert sum_func() == 15

    def test_add(self):
        assert add_func() == 2


if __name__ == '__main__':
    pytest.main(['-vs'])
```



## 2 Pytest 的一些插件

- pytest-html 使用--html=./report/report.html 生成html格式的自动化测试报告

- pytest-xdist 分布式执行 使用-n=number指定线程数

- pytest-ordering 改变测试用例的执行顺序

- pytest-rerunfailures 失败重试，使用 --reruns=number 设置重试测试

- allurre-pytest 更加美观的测试报告

## 3 Pytest 测试用例运行的方法

### 3.1 主函数模式

指定跟目录下所有的测试用例运行

```python
pytest.main(['-vs'])
```

指定运行某个文件

```python
pytest.main(["-vs"], "test_roles.py")
```

指定某个文件夹下的所有测试用例

```python
pytest.main(["-vs", "./filepath"])
```

使用多线程运行

```python
pytest.main(["-vs", "./filepath", "-n=2"])
```

每个用例失败重试两次

```python
pytest.main(["-vs", "./filepath", "--reruns=2"])
```

修改每个测试用例执行的顺序

```python
class TestCase:

    @pytest.mark.run(order=1)
    def test_sum(self):
        assert sum_func() == 11

    def test_add(self):
        assert add_func() == 1
```



参数解释：

- v：表述语输出调试信息， 包括打印的信息
- s：显示更详细的信息

- n：支持多线程或者分布式调用, 后面跟的数字代表开启的线程
- reruns：失败重试
- x：只要有一个错误，测试就停止
- --html filepath.html 报告路径
- -m "smoke or xx or xx..."  根据markers进行分组执行



### 3.2 命令行模式

```shell
pytest -vs ./filepath -n 2 --reruns 2
```



### 3.3 **配置文件模式 （pytest.ini）**

在项目中一般使用这种方式运行，一般在项目的根目录，编码模式要保证为`ANSI`的模式，使用命令模式或者函数模式都会读取这个配置文件。

```ini
[pytest]

# 命令行的参数
addopts = -vs  --alluredir=./tmp/my_allure_results

# 测试用例的路径
testpaths = ./testcase

# 模块名的规则
python_files = test_*.py

# 测试类名的规则
python_class = Test*

# 方法名的规则
python_functions = test_*

# 测试用例分组
markers =
    smoke: 冒烟测试
    loginTest: 登录测试
    userTest: 用户模块测试
```





### 3.4 测试用例分组的使用方法

使用`@pytest.mark.xx`， 这个`xx` 是指的是 `pytest.ini`中的markers中的选项，可以应用在函数上或者类上。

```python
class TestCase:

    @pytest.mark.run(order=1)
    def test_sum(self):
        assert sum_func() == 15

    @pytest.mark.smoke
    def test_add(self):
        assert add_func() == 2

    def test_login(self):
        assert add_func() == 2


@pytest.mark.loginTest
class TestCaseTwo:

    @pytest.mark.run(order=1)
    def test_two_sum(self):
        assert sum_func() == 15

    def test_two_add(self):
        assert add_func() == 2

    def test_two_login(self):
        assert add_func() == 2
```

执行指令

使用 or 可以制定多个分组的测试用例 

```shell
pytest -m "smoke or loginTest"
```



### 3.5 跳过测试用例

#### 3.5.1 有条件跳过

```python
@pytest.mark.skipif(age >= 18, reason="年龄大于等于18岁")
```

#### 3.5.2 无条件跳过

```python
@pytest.mark.skip(reason="跳过原因")
```



## 4 Pytest 参数化和读取yaml

参数化的使用示例：

```py
import pytest


# 单参数
@pytest.mark.parametrize("name", ["张三", "李四", "王五"])
def test_param(name, age):
    print(f"当前的姓名：{name}，年龄：{age}")

    assert name == "李四"


# 多参数
@pytest.mark.parametrize("name, age", [["张三", 18], ["李四", 25], ["王五", 30]])
def test_params(name, age):
    print(f"当前的姓名：{name}，年龄：{age}")

    assert name == "张三"


if __name__ == '__main__':
    pytest.main()
```



读取yaml的实例：

```python
import pytest


def get_data():
    """读取yaml中数据"""
    return []


# 单参数
@pytest.mark.parametrize("name", get_data())
def test_param(name, age):
    print(f"当前的姓名：{name}，年龄：{age}")

    assert name == "李四"


if __name__ == '__main__':
    pytest.main()
```





> 此处仅展示最基本的使用方法。



## 5 测试前后的准备和善后工作

### 5.1 steup和teardown

Pytest中的 steup 和 teardown 用法，分别有四种级别

| 级别     | 表示方法                              | 说明                                                         |
| :------- | ------------------------------------- | ------------------------------------------------------------ |
| 模块级别 | setup_module<br />teardown_module     | 在Python的一个模块级别执行，开始于模块的始末，只执行一次。   |
| 函数级别 | setup_function<br />teardown_function | 对每个函数用例生效, 每个测试用例函数执行前后执行。（不在类中） |
| 类级别   | setup_class<br />teardown_class       | 只在测试类的前后运行一次。                                   |
| 方法级别 | setup_method<br />teardown_method     | 开始于累的方法始末。每个类中的测试用例都会执行。             |

#### 5.1.1 模块级别示例

```python
import pytest


def setup_module():
    print("模块级别的前置操作...")


def test_1():
    print("测试1")
    assert 1 == 1


def test_2():
    print("测试2")
    assert 2 == 2


def teardown_module():
    print("模块级别的后置操作...")


if __name__ == '__main__':
    pytest.main()
```



#### 5.1.2 函数级别示例：

```python
import pytest


def setup_function():
    print("函数级别的前置操作...")


def test_1():
    print("测试1")
    assert 1 == 1


def test_2():
    print("测试2")
    assert 2 == 2


def teardown_function():
    print("函数级别的后置操作...")


if __name__ == '__main__':
    pytest.main(["-vs"])
```

#### 4.1.3 类级别

```python
import pytest


class TestClass:

    def setup_class(self):
        print("类级别的前置操作...")

    def test_1(self):
        print("测试1")
        assert 1 == 1

    def test_2(self):
        print("测试2")
        assert 2 == 2

    def teardown_class(self):
        print("类级别的后置操作...")


if __name__ == '__main__':
    pytest.main(["-vs"])
```



#### 4.1.4 方法级别的实例

```python
import pytest


class TestClass:

    def setup_method(self):
        print("方法级别的前置操作...")

    def test_1(self):
        print("测试1")
        assert 1 == 1

    def test_2(self):
        print("测试2")
        assert 2 == 2

    def teardown_method(self):
        print("方法级别的后置操作...")


if __name__ == '__main__':
    pytest.main(["-vs"])
```



### 5.2 fixture

Fixture是一个特殊的函数，用于提供测试用例所需的数据、对象或者其他资源或者做一些清理的操作。Fixture可以被多个测试用例共享，并且可以在测试用例中通过参数传递的方式使用。

Fixture相比于setup和teardown的优势：命名更加灵活，我们可以随意的定义函数指定为fixture，或者作用于不同的域中，或者是数据共享，也可以动态生成数据，并且可以通过参数传递来使用。Fixture的优势在于提高了测试用例的可重用性和可维护性。



#### 5.2.1 定义方方式

在自定义个的一个方法上面添加装饰器

```python
@pytest.fixture(scope="function", autouse=True, name="my_fixture", params=[1, 2], ids=[])
def my_fixture():
    print("【前置操作】自定义的fixture操作...")
    yield
    print("【后置操作】...作操erutxif的义定自")
```



#### 5.2.2 参数详解

scope 参数详解：

- `function`：默认范围，在函数测试用例前后执行。
- `class`：在类的测试用例前后执行。
- `module`：在模块的测试用例前后执行。
- `package`：在包的测试用例前后执行。
- `session`：在会话的测试用例前后执行。

socpe 参数也是一个可调用的对象，来动态的设置域。

autouse 参数：默认为fasle, 所有的测试都不显示使用自定义的fixture，当为true时，所有的测试都自动使用该fixture。

name 参数：默认为函数名，目的是给fixture起一个别名， 设置别名以后原来的函数名无法使用。

params 参数：支持可迭代对象，多个参数将会执行多次测试用例，如果fixture有return 也会被当做参数传递给对应的测试用例。

```python
import pytest


@pytest.fixture(scope="function", name="my_fixture_alias", params=[1, 2])
def my_fixture(request):
    print("【前置操作】自定义的fixture操作...")
    yield request.param
    print("【后置操作】...作操erutxif的义定自")


def test_case(my_fixture):
    print(f"我是一个测试用例，接受的参数：{my_fixture}")
    assert 1 == 1


if __name__ == '__main__':
    pytest.main(["-vs"])
```



ids 参数：为每个参数设置别名



### 5.3 fixture 和 conftest.py 结合

conftest.py 目的是和fixture结合使用实现==全局或者模块级别全局==的配置处理，比如项目的全局登录，模块的全局处理，conftest.py文件名是不能更改的，我们将fixture写在conftest.py中 就可以实现==不用导入==具体的fixture方法，来使用fixture。



## 6 企业级测试报告 allure

 ### 6.1 windwos 安装 allure

1. 首先安装 [Scoop](https://scoop.sh/) 

   ```powershell
   Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
   ```

   ```powershell
   irm get.scoop.sh | iex
   ```

2. 在安装 [allure](https://docs.qameta.io/allure/#_about)

   ```powershell
   scoop install allure
   ```

3. 查看版本

   ```powershell
   allure --version
   ```

安装成功！



### 6.2 生成报告

1. 生成报告临时文件，使用--allure 指定临时文件

   ```powershell
   pytest --alluredir ./tmp/my_allure_results
   ```

2. 在本地启动一个web服务展示报告

   ```powershell
   allure serve ./tmp/my_allure_results
   ```

   

### 6.3 allure 报告的定制

| 使用方法          | 参数值   | 参数说明                                    |
| ----------------- | -------- | ------------------------------------------- |
| @allure.epic()    | epic描述 | 定义项目，当有多个项目时使用，往下是feature |
| @allure.feature() | 模块名称 | 用例按照模块区分                            |
| @allure.story()   |          |                                             |
| @allure.          |          |                                             |
| @allure.          |          |                                             |
| @allure.          |          |                                             |
| @allure.          |          |                                             |
| @allure.          |          |                                             |
| @allure.          |          |                                             |
| @allure.          |          |                                             |

​	![image-20230801230915506](https://tc.chaizz.com/tc/image-20230801230915506.png)

## 7 jenkins 集成

​	待续...







