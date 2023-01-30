---
title: Python第三方包Dynaconf
author: chaizz
date: 2023-1-30 14:40:25
tags: Dynaconf
categories: Dynaconf
photos: ["https://tc.chaizz.com/1c078eb22dca11ed9d170242ac190002.jpg"]
---

​    

<!--more-->

# Python第三方包Dynaconf的使用

不管开发什么项目，我们总会遇到不同的环境下有着不同的配置信息，对于这些配置有应用相关的信息，同时也会有一些比较隐私的配置，比如MySQL或者Redis的账号密码等等。

## 1 项目中常用的配置方式

- 类似flask中的app.config可以加载类的模式读取配置信息

  ```python
  import logging
  
  
  class Config:
      LOG_LEVEL = logging.DEBUG
  
      
  class DevConfig(Config):
      pass
  
  
  class TestConfig(Config):
      pass
  
      
  class ProdConfig(Config):
      LOG_LEVEL = logging.INFO
      
  ```

- 或者使用python-dotenv， 从.env 中加载配置信息，且是遵循了[12-factor application](https://12factor.net/config)原则。具体配置不在赘述。

- 使用Dynaconf， Dynaconf同样是受到了[12-factor application](https://12factor.net/config)的启发，但是相对于dotenv 提供了多种不同的配置方式。并且可以兼容python-dotenv。



## 2 Dynaconf的基础用法

[Dynaconf](https://www.dynaconf.com/)的Slogan就是***Configuration Management for Python.***

### 2.1 Dynaconf的基本特征(内容来自官网)：

- 受到了[12-factor application](https://12factor.net/config)的启发。
- 设置管理（默认值、验证、解析、模板）。
- 多种文件格式（toml、yaml、json、ini、py）。
- 支持环境变量覆盖，支持dotenv。
- 可以用于多环境。（default, development, testing, production）。
- 内置支持 Hashicorp Vault 和 Redis 作为设置和秘密存储。
- **Django**和**Flask**网络框架的内置扩展。
- 常见操作的 CLI，例如`init, list, write, validate, export`。

### 2.2 Dynaconf安装

```python
pip install dynaconf
```

### 2.3 Dynaconf初始化

```shell
# 移动到项目根目录下
cd your/project/

# 初始化dynaconf相关配置文件
# dynaconf 支持多种文件，默认使用toml格式，也可以指定其他的格式（在Django）项目中建议使用yaml格式。
dynaconf init -f toml
```

正常输出如下信息：

```shell
⚙️  Configuring your Dynaconf environment
------------------------------------------
🐍 The file `config.py` was generated.
  on your code now use `from config import settings`.
  (you must have `config` importable in your PYTHONPATH).

🎛️  settings.toml created to hold your settings.

🔑 .secrets.toml created to hold your secrets.

🙈 the .secrets.toml is also included in `.gitignore`
  beware to not push your secrets to a public repo
  or use dynaconf builtin support for Vault Servers.

🎉 Dynaconf is configured! read more on https://dynaconf.com
   Use `dynaconf -i config.settings list` to see your settings
```

在项目文件夹中生成了如下四个文件：

```shell

    目录: D:\Chaizz\Project\Python\dyna


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         2023/1/30     15:12                __pycache__
-a----         2023/1/30     15:12             46 .gitignore
-a----         2023/1/30     15:12              0 .secrets.toml
-a----         2023/1/30     15:12            270 config.py
-a----         2023/1/30     15:12              0 settings.toml
```

.gitignore      ：上传到代码仓库标记需要忽略的文件

.secrets.toml   ：存放隐私配置

config.py       ：实例化Dynaconf 对象

settings.toml   ：项目的常规配置



### 2.4 在python中使用示例

config.py

```python
from dynaconf import Dynaconf, Validator

settings = Dynaconf(
    # 取消 Dynaconf 的前缀
    envvar_prefix=False,
    # 指定包含配置的文件
    settings_files=['settings.toml', '.secrets.toml'],
    # 启用分层环境，即下文中的 dev、test、prod
    environments=True,
    # 自定义验证器
    validators=[
        # 确保某些参数存在（必需）
        Validator('VERSION', must_exist=True),
        # 确保变量的类型， 为特定的类型
        Validator('MYSQL_PORT', is_type_of=int),
        # 确保某些参数不存在
        Validator('PASSWORD', must_exist=False),
        # 保证参数满足一些条件
        # conditions: (eq, ne, lt, gt, lte, gte, identity, is_type_of, is_in, is_not_in)
        Validator('AGE', lte=30, gte=10),
        # 在某个环境中确保一些参数等于某个值
        Validator('PROJECT', eq='hello_world', env='prod'),
        # 保证参数（字符串）满足一些条件
        # conditions: (len_eq, len_ne, len_min, len_max, cont)
        # 确定值的最小最大长度
        Validator("NAME", len_min=3, len_max=125),
        # 确保，字符串在集合中存在
        Validator("DEV_SERVERS", cont='localhost'),
        # 确保参数的长度和定义的长度相等
        Validator("PORT", len_eq=4),
    ]
)
```

settings.toml

```toml

[default]
# 当 Dynaconf的属性：environments 为True，default 为默认属性。
VERSION = "default"
AGE = 15
NAME = "admin"


[dev]
VERSION = "0.0.1"
AGE = 190
NAME = "admin"


[test]
VERSION = "1.0.0"
AGE = 150
NAME = "admin"


[prod]
VERSION = "2.0.0"
AGE = 3
PROJECT = "hello_world"
NAME = "admin"
```

.secrets.toml

```toml
[default]
# 当 Dynaconf的属性：environments 为True，default 为默认属性。
MYSQL_HOST = "default"
MYSQL_PORT = 3306
MYSQL_USER = "default"
MYSQL_PASSWORD = "default"
MYSQL_DATABASE = "default"


[dev]
MYSQL_HOST = "1.1.1.1"
MYSQL_PORT = 3306
MYSQL_USER = "admin"
MYSQL_PASSWORD = "PASSWORD"
MYSQL_DATABASE = "database"


[test]
MYSQL_HOST = "22222222"
MYSQL_PORT = 3306
MYSQL_USER = "admin"
MYSQL_PASSWORD = "test"
MYSQL_DATABASE = "database"


[prod]
MYSQL_HOST = "333333333333"
MYSQL_PORT = 3306
MYSQL_USER = "admin"
MYSQL_PASSWORD = "prod"
MYSQL_DATABASE = "database"
```



在上面的toml文件中：dev、test、prod 代表不同的开发环境下的不同的配置信息，需要在系统环境变量中设置 `export ENV_FOR_DYNACONF = dev|test|prod` 。

default是当Dynaconf的属性environments为True时, 设置的默认值。

**在不同的开发环境之间切换只需要在环境变量中设置`ENV_FOR_DYNACONF`的值即可。**



## 3 Dynaconf的高级用法

### 3.1 hooks：需要根据先前加载的设置，有条件地加载数据。



### 3.2 PrefixFilter：根据前缀过滤

```python
from dynaconf import Dynaconf
from dynaconf.strategies.filtering import PrefixFilter

settings = Dynaconf(
    settings_file="settings.toml",
    environments=False
    # 初始化Dynaconf时，添加过滤条件，仅加载前缀为prefix_开头的配置。
    filter_strategy=PrefixFilter("prefix")
)
```



### 3.3 切换工作环境

#### 3.3.1 from_env：返回指定环境的配置

在配置文件 srttings.toml 中有如下配置。

```toml
[development]
message = 'This is in dev'
foo = 1
[other]
message = 'this is in other env'
bar = 2
```

在正常使用时

```python
>>> from dynaconf import settings
>>> print(settings.MESSAGE)
'This is in dev'
>>> print(settings.FOO)
1
>>> print(settings.BAR)
AttributeError: settings object has no attribute 'BAR'
```

然后你可以使用`from_env`：

```python
>>> print(settings.from_env('other').MESSAGE)
'This is in other env'
>>> print(settings.from_env('other').BAR)
2
>>> print(settings.from_env('other').FOO)
AttributeError: settings object has no attribute 'FOO'
```

也可以分别获取不同的环境下配置：

```python
development_settings = settings.from_env('development')
other_settings = settings.from_env('other')
```



#### 3.3.2 setenv：在原地更改配置。

```python
from dynaconf import settings

settings.setenv('other')
# 将当前的环境更改为 other 环境
assert settings.MESSAGE == 'This is in other env'

settings.setenv()
# 将当前环境返回到先前的环境
```



#### 3.3.3 using_env：使用上下文管理器切换环境

```python
from dynaconf import settings

with settings.using_env('other'):
    # 在上下文管理器之内，切换为选择的环境
    assert settings.MESSAGE == 'This is in other env'

# 在上下文管理器之外，还是默认的环境
assert settings.MESSAGE == 'This is in dev'
```



### 3.4 导出配置信息

1. 使用命令导出 `dynaconf list -o /path/to/file.yaml|toml|ini|json|py`

2. 使用代码导出

   ```python
   from dynaconf import loaders
   from dynaconf import settings
   from dynaconf.utils.boxing import DynaBox
   
   # generates a dict with all the keys for `development` env
   data = settings.as_dict(env='development')
   
   # writes to a file, the format is inferred by extension
   # can be .yaml, .toml, .ini, .json, .py
   loaders.write('../file.json', DynaBox(data).to_dict(), env='development')
   ```

## 4 Dynaconf的详细配置

[Dynaconfg详细配置](https://www.dynaconf.com/configuration/)



















































