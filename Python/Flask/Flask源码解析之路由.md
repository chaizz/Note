---
title: Flask源码解析之路由（FlaskV3.0.x）
author: chaizz
date: 2023-11-7
tags: Flask源码解析
categories: 
  - 源码解析
  - Flask
photos: ["https://origin.chaizz.com/tc/flask-horizontal.png"]
cover: "https://origin.chaizz.com/tc/flask-horizontal.png"
---

​                  

<!--more-->

# Flask源码解析之路由



> 须知：
>
> - Flask 版本 3.0.x 
>
> Flask 在3.0 版本对代码进行了重构，使 Flask 和 Blueprint 类具有 Sans-IO 基础。

 ## 1. FBV 模式

我们从最小的`Flask`程序开始入手

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"


if __name__ == '__main__':
    app.run()
```



路由是使用`app.route`使用装饰器的方式进行声明，装饰器的执行逻辑：

1. 先执行 `app.route('/')` ，得到一个返回值是一个函数`decorator`。

```python
# 源码内容：

class Scaffold:
    # 省略 ... 
    
    @setupmethod
    def route(self, rule: str, **options: t.Any) -> t.Callable[[T_route], T_route]:
        # 省略 ... 
        def decorator(f: T_route) -> T_route:
            endpoint = options.pop("endpoint", None)
            self.add_url_rule(rule, endpoint, f, **options)
            return f

        return decorator
```

我们可以看到`app.route`点击进去跳转到了`Scaffold `这个类中的`route`方法，在`Flask 3.0`版本中，源码进行了比较大的更改，不过不影响核心流程。

既然能跳转到另外的一个类中， 那么`app`对象肯定也继承了此类，否则无法跳转过来， 我们可以再看看`Flask`的源码。

```python
class Flask(App):
	# 省略 ... 
```

`Flask` 继承了`App`这个类， 我们再看`App`这个类的源码。

```python
class App(Scaffold):
    # 省略 ... 
```

正向我们前面所说的， `Flask `类使用多重继承，继承了`Scaffold`，所以我们点击`app.route`进入到了`Scaffold`这个类的`route`方法。

2. 我们继续看，`route`方法返回值得到一个函数`decorator`。那么整个路由可以写为以下的格式。

```python
@decorator
def hello_world():
    return "<p>Hello, World!</p>"
```

3. 接下来再执行` decorator(hello_world)`，那么`hello_world`这个方法就会变成`decorator`返回的对象。在`decorator`的内部有两行代码，第一行是获取`endpoint`参数。

```python
endpoint = options.pop("endpoint", None)
self.add_url_rule(rule, endpoint, f, **options)
```

接下来我们再看`add_url_rule`的方法， 我们还是先从`Flask`，类内部开始看，`Flask`类内部没有实现，我们再找`App`类，`App`类内部实现了这个方法：

```python
class App(Scaffold):

    url_rule_class = Rule
    url_map_class = Map
    
    
	# 省略 ...
    
    def __init(...):
        # 省略 ...
    	self.url_map = self.url_map_class(host_matching=host_matching)
    
    @setupmethod
    def add_url_rule(
        self,
        rule: str,
        endpoint: str | None = None,
        view_func: ft.RouteCallable | None = None,
        provide_automatic_options: bool | None = None,
        **options: t.Any,
    ) -> None:
        
        # 判断endpoint， 如果为空，那么就吧视图函数的函数名当做 endpoint
        if endpoint is None:
            endpoint = _endpoint_from_view_func(view_func)  # type: ignore
        options["endpoint"] = endpoint
        methods = options.pop("methods", None)

        # ============== 对请求方法进行处理 开始 ============== 
        if methods is None:
            methods = getattr(view_func, "methods", None) or ("GET",)
        if isinstance(methods, str):
            raise TypeError(
                "Allowed methods must be a list of strings, for"
                ' example: @app.route(..., methods=["POST"])'
            )
        methods = {item.upper() for item in methods}

        # Methods that should always be added
        required_methods = set(getattr(view_func, "required_methods", ()))

        # starting with Flask 0.8 the view_func object can disable and
        # force-enable the automatic options handling.
        if provide_automatic_options is None:
            provide_automatic_options = getattr(
                view_func, "provide_automatic_options", None
            )

        if provide_automatic_options is None:
            if "OPTIONS" not in methods:
                provide_automatic_options = True
                required_methods.add("OPTIONS")
            else:
                provide_automatic_options = False
                
        methods |= required_methods
        # ============== 对请求方法进行处理 结束 ============== 
        
        # 这里实例化了一个Rule对象rule, 此处我们先不管 Rule 对象的内部实现。
        rule = self.url_rule_class(rule, methods=methods, **options)
        rule.provide_automatic_options = provide_automatic_options  # type: ignore
		 
        # 此处 url_map 是一个Map对象，包含了所有的rule对象。调用add方法将rule对象即Map中
        self.url_map.add(rule)
        if view_func is not None:
            old_func = self.view_functions.get(endpoint)
            if old_func is not None and old_func != view_func:
                raise AssertionError(
                    "View function mapping is overwriting an existing"
                    f" endpoint function: {endpoint}"
                )
            self.view_functions[endpoint] = view_func
```



所以再设置路由时，可以通过 `add_url_rule`设置或者是通过装饰器设置，装饰器在内部也是调用了 `add_url_rule`。

```python
# 装饰器方式
@app.route("/")
def hello_world():
    return "首页"


def index():
    return 'index'

# add_url_rule 方式
app.add_url_rule (rule='/index', endpoint='index', view_func=index)
```



动态路由

```python
@app.route('/item/<int:id>')
def item(): ...
```

当路由是动态路由时，在上文的`self.url_map.add(rule)`时，add方法中包含`rule.bind()`，在路由绑定时，会解析路由并获取装换器。默认的转换器有：

```python
DEFAULT_CONVERTERS: t.Mapping[str, type[BaseConverter]] = {
    "default": UnicodeConverter,
    "string": UnicodeConverter,
    "any": AnyConverter,
    "path": PathConverter,
    "int": IntegerConverter,
    "float": FloatConverter,
    "uuid": UUIDConverter,
}
```



当我们需要自定义时，我们可以根据上述的转换器，进行自定义动态路由。

`Flask`规定的一些转换器的格式都继承自：`BaseConverter`， 而且`Map`类所接受`converters`参数类型也是`BaseConverter`的子类，所以我们自定义时都要继承自`BaseConverter`。![image-20231108222521379](https://origin.chaizz.com/tc/image-20231108222521379.png)



自定义正则转换器：

```python
import typing as t

from flask import Flask
from werkzeug.routing import BaseConverter, Map

app = Flask(__name__)


class RegexConverter(BaseConverter):
    """
    自定义Converter
    """

    def __init__(self, map: Map, regex: t.Any) -> None:
        super().__init__(map)
        self.regex = regex


# 添加转化器
app.url_map.converters['regex'] = RegexConverter


@app.route("/item/<regex('\w+'):item>")
def item(item: t.Any) -> str:
    print(item)
    return "正则格式的路由"


if __name__ == '__main__':
    app.run()
```



以上的路由操作都是使用装饰器`FBV`的方式，当我们使用`CBV`的方式去写路由视图时，具体的流程又是怎么走的呢？

## 2. CBV 模式

一个最小的`CBV`的例子

```python
from flask import Flask
from flask.views import MethodView

app = Flask(__name__)


class ItemView(MethodView):
    methods = ['GET', "POST"]

    def get(self):
        print('get 请求')
        return 'get'

    def post(self):
        print('post 请求')
        return 'post'


app.add_url_rule('/item', view_func=ItemView.as_view('item'))

if __name__ == '__main__':
    app.run()
```

我们进入`MethodView`的`as_view`源码， 他返回的时是一个`view`函数，即`view_func=view`，所以请求过来以后，执行`view`方法，在`view`的内部实例化了`view.view_class`，也就是当前类本身即`ItemView`。

```python
@classmethod
def as_view(
    cls, name: str, *class_args: t.Any, **class_kwargs: t.Any
) -> ft.RouteCallable:

    if cls.init_every_request:

        def view(**kwargs: t.Any) -> ft.ResponseReturnValue:
            self = view.view_class(  # type: ignore[attr-defined]
                *class_args, **class_kwargs
            )
            return current_app.ensure_sync(self.dispatch_request)(**kwargs)

    else:
        self = cls(*class_args, **class_kwargs)

        def view(**kwargs: t.Any) -> ft.ResponseReturnValue:
            return current_app.ensure_sync(self.dispatch_request)(**kwargs)
	
    # CBV 的装饰器处理方式
    if cls.decorators:
        view.__name__ = name
        view.__module__ = cls.__module__
        for decorator in cls.decorators:
            view = decorator(view)

    # We attach the view class to the view function for two reasons:
    # first of all it allows us to easily figure out what class-based
    # view this thing came from, secondly it's also used for instantiating
    # the view class so you can actually replace it with something else
    # for testing purposes and debugging.
    view.view_class = cls  # type: ignore
    view.__name__ = name
    view.__doc__ = cls.__doc__
    view.__module__ = cls.__module__
    view.methods = cls.methods  # type: ignore
    view.provide_automatic_options = cls.provide_automatic_options  # type: ignore

    # 返回的是一个 view 函数
    return view
```



然后我们再看， 在`view`中调用了`current_app.ensure_sync`， 我们暂且理解为处理异步代码的操作。它内部返回了接受的函数，而且后面跟了`(**kwargs)`，说明`dispatch_request`被执行。

```python
def ensure_sync(self, func: t.Callable) -> t.Callable:
    """Ensure that the function is synchronous for WSGI workers.
    Plain ``def`` functions are returned as-is. ``async def``
    functions are wrapped to run and wait for the response.

    Override this method to change how the app runs async views.

    .. versionadded:: 2.0
    """
    if iscoroutinefunction(func):
        return self.async_to_sync(func)

    return func
```

然后我们去查找`dispatch_request`方法， `ItemView`没有，再找`MethodView`， `MethodView`是有的。

```python
def dispatch_request(self, **kwargs: t.Any) -> ft.ResponseReturnValue:
    meth = getattr(self, request.method.lower(), None)

    # If the request method is HEAD and we don't have a handler for it
    # retry with GET.
    if meth is None and request.method == "HEAD":
        meth = getattr(self, "get", None)

    assert meth is not None, f"Unimplemented method {request.method!r}"
    return current_app.ensure_sync(meth)(**kwargs)
```

我可能可以看到是和`Django`的`CBV`是一致的。

首先通过反射，获取当前实例下的请求方法对象的函数，如果没有或者是`HEAD`则自动设置为`get`方法，然后直接调用该类下的方法`meth(**kwargs)`。



在as_view视图执行的时候， 我们可以看到这样一段代码

```python
if cls.decorators:
    view.__name__ = name
    view.__module__ = cls.__module__
    for decorator in cls.decorators:
        view = decorator(view)
```

此段代码正是处理CBV的请求装饰器的作用， 我们可以通过这种方式来给CBV请求添加装饰器。

```python
from flask import Flask
from flask.views import MethodView

app = Flask(__name__)


def wrapper(func):
    def inner(*args, **kwargs):
        print('开始...1')
        ret = func(*args, **kwargs)
        print('开始...1')
        return ret

    return inner


def wrapper2(func):
    def inner(*args, **kwargs):
        print('开始...2')
        ret = func(*args, **kwargs)
        print('开始...2')
        return ret

    return inner


class ItemView(MethodView):
    methods = ['GET', "POST"]
    decorators = [wrapper, wrapper2]

    def get(self):
        print('get 请求')
        return 'get'

    def post(self):
        print('post 请求')
        return 'post'


app.add_url_rule('/item', view_func=ItemView.as_view('item'))

if __name__ == '__main__':
    app.run()
```

wrapper, wrapper2 的执行顺序分别是:

```python
# 开始...2
# 开始...1
# get 请求
# 开始...1
# 开始...2
```





