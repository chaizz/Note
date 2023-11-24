---
title: Flask源码解析之启动流程（FlaskV3.0.x）
author: chaizz
date: 2023-11-10
tags: Flask源码解析
categories: 
  - 源码解析
  - Flask
photos: ["https://origin.chaizz.com/tc/flask-horizontal.png"]
cover: "https://origin.chaizz.com/tc/flask-horizontal.png"
---

​                  

<!--more-->

# Flask源码解析之启动流程

## 1. 最小的 Flask 程序

```python
# app.py

from flask import Flask # ①

app = Flask(__name__) # ②

@app.route("/") # ③
def hello_world():
    return "<p>Hello， World!</p>"


if __name__ == '__main__':
    app.run() # ④
```

我们先从使用代码启动的方式来看， 暂时不看使用命令行的方式启动的过程。

我们安装代码的执行顺序从上往下看

①第一步 导入`Flask`对象。

②实例化`app`对象。在内部执行类的`__init__`方法。

③创建路由和视图函数。

④执行 `app.run()`。

## 2. 查看 app 源码 

我们进入`app`的源码中查看`__init__`发生了什么（其他的一些类属性先不管）。

```python
class Flask(App):
    def __init__(
        self，
        import_name: str，
        static_url_path: str | None = None，
        static_folder: str | os.PathLike | None = "static"， # ①
        static_host: str | None = None，
        host_matching: bool = False，
        subdomain_matching: bool = False，
        template_folder: str | os.PathLike | None = "templates"， # ①
        instance_path: str | None = None，
        instance_relative_config: bool = False，
        root_path: str | None = None，
    ):
        super().__init__(
            import_name=import_name，
            static_url_path=static_url_path，
            static_folder=static_folder，
            static_host=static_host，
            host_matching=host_matching，
            subdomain_matching=subdomain_matching，
            template_folder=template_folder，
            instance_path=instance_path，
            instance_relative_config=instance_relative_config，
            root_path=root_path，
        ) # ②

        if self.has_static_folder:
            assert (
                bool(static_host) == host_matching
            )， "Invalid static_host/host_matching combination"

            self_ref = weakref.ref(self)
            self.add_url_rule( # ③
                f"{self.static_url_path}/<path:filename>"，
                endpoint="static"，
                host=static_host，
                view_func=lambda **kw: self_ref().send_static_file(**kw)，
            )
```

在①处`Flask`设置了静态文件默认路径和模板文件的路径。

在②处执行了父类的`init`文件。

在③的地方添加了静态文件的路由， 所以`Flask`在初始化的时候，我们不指定配置文件时，会有默认的路由创建。

## 3. 我们再看 app.run()的方法

```python
class Flask(App):    
    def run(
        self，
        host: str | None = None，
        port: int | None = None，
        debug: bool | None = None，
        load_dotenv: bool = True，
        **options: t.Any，
    ) -> None:
        “”“
         本地开发的启动方式， 不要再生产服务器使用的一些注意事项
        ”“”
        # ... 
        
        from werkzeug.serving import run_simple

        try:
            run_simple(t.cast(str， host)， port， self， **options) # ①
        finally:
            self._got_first_request = False
```

在①的上面都是针对`host`、`port`以及环境变量的的一些配置。`run`方法中真正执行的是`werkzeug`的`run_simple`方法。他接受了一个`host`参数，一个`port`参数，和一个`self`， 这个`self`就是`Flask`本身的实例。

## 4. run_simple 的源码。

```python
def run_simple(
    hostname: str，
    port: int，
    application: WSGIApplication，
    use_reloader: bool = False，
    use_debugger: bool = False，
    use_evalex: bool = True，
    extra_files: t.Iterable[str] | None = None，
    exclude_patterns: t.Iterable[str] | None = None，
    reloader_interval: int = 1，
    reloader_type: str = "auto"，
    threaded: bool = False，
    processes: int = 1，
    request_handler: type[WSGIRequestHandler] | None = None，
    static_files: dict[str， str | tuple[str， str]] | None = None，
    passthrough_errors: bool = False，
    ssl_context: _TSSLContextArg | None = None，
) -> None:
    """
    在内部启动了WSGI服务器， 即socket服务， 然后等待用户访问具体的路由。
    """
    if not isinstance(port， int):
        raise TypeError("port must be an integer")

    if static_files:
        from .middleware.shared_data import SharedDataMiddleware

        application = SharedDataMiddleware(application， static_files)

    if use_debugger:
        from .debug import DebuggedApplication

        application = DebuggedApplication(application， evalex=use_evalex)

    if not is_running_from_reloader():
        fd = None
    else:
        fd = int(os.environ["WERKZEUG_SERVER_FD"])

    srv = make_server(
        hostname，
        port，
        application，
        threaded，
        processes，
        request_handler，
        passthrough_errors，
        ssl_context，
        fd=fd，
    ) # ①
    srv.socket.set_inheritable(True)
    os.environ["WERKZEUG_SERVER_FD"] = str(srv.fileno())

    if not is_running_from_reloader():
        srv.log_startup()
        _log("info"， _ansi_style("Press CTRL+C to quit"， "yellow"))

    if use_reloader:
        from ._reloader import run_with_reloader

        try:
            run_with_reloader(
                srv.serve_forever，
                extra_files=extra_files，
                exclude_patterns=exclude_patterns，
                interval=reloader_interval，
                reloader_type=reloader_type，
            )
        finally:
            srv.server_close()
    else:
        srv.serve_forever() # ②

```

在①处滴调用`make_server`方法返回了实例化后的`WSGI`服务对象，并在②处调用`serve_forever`方法，启动服务等待接受请求。而`WSGI`对象内部实现了一个`socket`服务，他继承了`HTTPServer`，`HTTPServer`又继承了`socketserver.TCPServer`。



## 5. make_server 

不考虑其他的情况，我们看到返回了一个实例化的对象

```python

def make_server(
    host: str，
    port: int，
    app: WSGIApplication，
    threaded: bool = False，
    processes: int = 1，
    request_handler: type[WSGIRequestHandler] | None = None，
    passthrough_errors: bool = False，
    ssl_context: _TSSLContextArg | None = None，
    fd: int | None = None，
) -> BaseWSGIServer:
    
    return BaseWSGIServer(
        host， port， app， request_handler， passthrough_errors， ssl_context， fd=fd
    )
```

其实到这里`Flask`启动的流程已经大致结束了，但是我们可以结合上一篇文章《Flask源码解析之路由》来看看一个路由到来的整个流程。

## 6. 整个服务启动请求流程

### 6.1前置知识

#### 6.1.1 TCP 服务的执行流程

我们可以通过查看`Python`的文档，去看看如何实现一个`TCPServer`。

![image-20231113103006592](https://origin.chaizz.com/tc/image-20231113103006592.png)

在启动一个`TCPServer`的时候他接受一个继承`BaseHTTPRequestHandler的Handler`对象，然后当请求到来的时候执行内部的`handle`方法。具体的`TCPServer`执行流程大致如下：

#### 6.1.2 with语句

通过`with`语句创建`tcp`对象，初始化一些参数，创建`socket`对象，绑定地址和开启监听。

```python
class TCPServer(BaseServer):

    def __init__(self， server_address， RequestHandlerClass， bind_and_activate=True):
        # 执行父类的初始化方法
        BaseServer.__init__(self， server_address， RequestHandlerClass)
        # ...
        self.server_bind()
        self.server_activate()
        
	def server_bind(self): ...
    
    def server_activate(self): ...
```

#### 6.1.3 serve_forever 方法

父类的`with`语句`__enter__`方法返回`self`， 调用`self.serve_forever`方法，`TCPServer`未实现所以查找`BaseServer`的`serve_forever`方法。

```python
class BaseServer:
    
    def __init__(self， server_address， RequestHandlerClass):
        """Constructor.  May be extended， do not override."""
        self.server_address = server_address
        self.RequestHandlerClass = RequestHandlerClass
        self.__is_shut_down = threading.Event()
        self.__shutdown_request = False
        
    def serve_forever(self， poll_interval=0.5):
        self.__is_shut_down.clear()
        try:
            with _ServerSelector() as selector:
                selector.register(self， selectors.EVENT_READ)

                while not self.__shutdown_request:
                    ready = selector.select(poll_interval)
                    if self.__shutdown_request:
                        break
                    if ready:
                        # 执行此方法，获取请求
                        self._handle_request_noblock()

                    self.service_actions()
        finally:
            self.__shutdown_request = False
            self.__is_shut_down.set()
```

#### 6.1.4 _handle_request_noblock 方法

在`serve_forever`中执行了`_handle_request_noblock`方法获取请求。

```python
class BaseServer:

    def _handle_request_noblock(self):
        try:
            # 获取socket接受的对象
            request， client_address = self.get_request()
        except OSError:
            return
        # 验证请求
        if self.verify_request(request， client_address):
            try:
                # 处理请求
                self.process_request(request， client_address)
            except Exception:
                self.handle_error(request， client_address)
                self.shutdown_request(request)
            except:
                self.shutdown_request(request)
                raise
        else:
            self.shutdown_request(request)
            
            
    def process_request(self， request， client_address):
        self.finish_request(request， client_address)
        self.shutdown_request(request)
            
            
    def finish_request(self， request， client_address):
        # 调用 处理请求类。
        self.RequestHandlerClass(request， client_address， self)
```

#### 6.1.5 RequestHandlerClass

接下来我们再看`TCPServer`中的`RequestHandlerClass`是如何实现的。在上面`Python`官方文档示例中：`TCPServer`自定义了一个`MyTCPHandler`他继承了`socketserver.BaseRequestHandler`。我们查看这个类的实现方式。

```python
class BaseRequestHandler:
    “”“
    	这个类是为要处理的每个请求实例化的。构造函数设置实例变量request、client_address 			server，然后调用handle（）方法。要实现一个特定的服务，您所需要做的就是派生一个定义			handle（）方法的类。
    “”“

    def __init__(self， request， client_address， server):
        self.request = request
        self.client_address = client_address
        self.server = server
        self.setup()
        try:
            # 执行handle 方法
            self.handle 方法()
        finally:
            self.finish()

    def setup(self):
        pass

    def handle(self):
        pass

    def finish(self):
        pass
```

我们可以根据注释了解到，他是一个专门处理请求类的基类，我们要自定义`TCPServcer`，就要继承他实现一个子类。在他的`init`方法中调用`handle`方法。

这下我们终于知道了为什么我们自己写一个`TCPServer`时，为什么要自己实现一个`Handler`类，且要实现`handle`方法了。

## 7. WSGIRequestHandler

让我们再回到`Flask`的源码中，在`BaseWSGIServer`中同样有属性是`handle`， 他的的类型是，`WSGIRequestHandler`。但是他不是直接继承`socketserver.BaseRequestHandler`而是`socketserver.StreamRequestHandler`， 其实也是一样的，只不过`StreamRequestHandler`在对`BaseRequestHandler`封装了一层而已。接下来我们看`WSGIRequestHandler`的源码。

```python
class WSGIRequestHandler(BaseHTTPRequestHandler):
    def handle(self) -> None:
        try:
            # 调用了父类的 handle 方法
            super().handle()
        except (ConnectionError， socket.timeout) as e:
            self.connection_dropped(e)
        except Exception as e:
            if self.server.ssl_context is not None and is_ssl_error(e):
                self.log_error("SSL error occurred: %s"， e)
            else:
                raise
```

在`handle`中调用了父类的`handle`方法，我们再看父类的`handle`方法。执行了`handle_one_request`方法。

```python
class BaseHTTPRequestHandler(socketserver.StreamRequestHandler):
    
    def handle_one_request(self):
        try:
            self.raw_requestline = self.rfile.readline(65537)
            if len(self.raw_requestline) > 65536:
                self.requestline = ''
                self.request_version = ''
                self.command = ''
                self.send_error(HTTPStatus.REQUEST_URI_TOO_LONG)
                return
            if not self.raw_requestline:
                self.close_connection = True
                return
            if not self.parse_request():
                # An error code has been sent， just exit
                return
            mname = 'do_' + self.command
            if not hasattr(self， mname):
                self.send_error(
                    HTTPStatus.NOT_IMPLEMENTED，
                    "Unsupported method (%r)" % self.command)
                return
            
            # 通过 getattr 获取 mname， 然后执行 mname 方法
            method = getattr(self， mname)
            method()
            self.wfile.flush() #actually send the response if not already done.
        except TimeoutError as e:
            #a read or a write timed out.  Discard this connection
            self.log_error("Request timed out: %r"， e)
            self.close_connection = True
            return
    
    
    def handle(self):
        """Handle multiple requests if necessary."""
        self.close_connection = True

        self.handle_one_request()
        while not self.close_connection:
            self.handle_one_request()
```

### 7.1  _\_getattr__ 方法

此方法中重要的一段代码是执行了`method`方法。这个方法是通过`getattr`获取的。 `getattr`方法会执行 类中的`__getattr__`方法。

```python
class WSGIRequestHandler(BaseHTTPRequestHandler):    
    def __getattr__(self， name: str) -> t.Any:
        # All HTTP methods are handled by run_wsgi.
        if name.startswith("do_"):
            return self.run_wsgi

        # All other attributes are forwarded to the base class.
        return getattr(super()， name)
```

### 7.2 run_wsgi 方法

它又调用了`run_wsgi`方法，再看`run_wsgi`的方法。

```python
class WSGIRequestHandler(BaseHTTPRequestHandler):    
    def run_wsgi(self) -> None:
		# ...
        def execute(app: WSGIApplication) -> None:
            application_iter = app(environ， start_response)
            # ...
        try:            
            # ================= 重点 =================
            execute(self.server.app)
            # ================= 重点 =================
        except (ConnectionError， socket.timeout) as e:
            self.connection_dropped(e， environ)
        except Exception as e:
			# ...
```



### 7.3 execute方法

在`run_wsgi`方法中最终执行了`execute`方法，它接受了一个参数`self.server.app`， 我可以先看看这个`self.server.app`参数是谁。

```python
class WSGIRequestHandler(BaseHTTPRequestHandler):        
    def execute(app: WSGIApplication) -> None:
        application_iter = app(environ， start_response)
		# ...
```

### 7.4 excute 的参数 self.server.app

我们先回到【6.1.4 _handle_request_noblock 方法】步骤， 在`finish_request`方法中实例化了`self.RequestHandlerClass(request, client_address, self)` 这个 `RequestHandlerClass` 也就是我们现在看到的 `WSGIRequestHandler` ，但是他没有初始化的步骤，所以是继承了基类的初始化方法。我们看代码

```python
class BaseRequestHandler:
    def __init__(self， request， client_address， server):
        self.request = request
        self.client_address = client_address
        # 赋值 server 给 self.server
        self.server = server
        self.setup()
        try:
            self.handle()
        finally:
            self.finish()

class StreamRequestHandler(BaseRequestHandler):
    ...
    
class BaseHTTPRequestHandler(socketserver.StreamRequestHandler):
    ...
    
class WSGIRequestHandler(BaseHTTPRequestHandler):
    ...
```

这里的`self.server`正是`BaseServer`类或者是他的子类。`self.server.app`正是`BaseServer`的`app`属性。

我们再回到 第四步`run_simple`的源码中，它调用了`make_server`方法 创建了`BaseWSGIServer`实例，所以上面的这句话【 这里的`self.server`正是`BaseServer`类或者是他的子类】中指的类

就是`BaseWSGIServer`这个类。 我们看代码：

```python
class BaseServer:
    ...

class TCPServer(BaseServer):
    ...

class HTTPServer(TCPServer):
    ...

class BaseWSGIServer(HTTPServer):

    def __init__(
        self，
        host: str，
        port: int，
        app: WSGIApplication，
        handler: type[WSGIRequestHandler] | None = None，
        passthrough_errors: bool = False，
        ssl_context: _TSSLContextArg | None = None，
        fd: int | None = None，
    ) -> None:
        if handler is None:
            handler = WSGIRequestHandler

        self.app = app
```



所以`self.server.app`指的就是`BaseWSGIServer`中初始化传过来的`app`实参。 那`app`是谁呢， 就很显而易见了。

是由`run_simple(hostname， port， application，...) -->  make_server(hostname， port， application，...) -->  BaseWSGIServer(hostname， port， application，...)`

一步一步传过来的。

`run_simple`又是在`Flask`的`run`方法中调用的， 第三个参数正是`self`，即`Flask`实例对象。

```python
class Flask(App):
    def run(
        self，
        host: str | None = None，
        port: int | None = None，
        debug: bool | None = None，
        load_dotenv: bool = True，
        **options: t.Any，
    ) -> None:
        # ...
        from werkzeug.serving import run_simple
        try:
            run_simple(t.cast(str， host)， port， self， **options)
        finally:
            self._got_first_request = False
```

所以`excute`的参数`self.server.app`正是`Flask`实例对象。



我们再回到 【7.3 execute方法】，在其内部执行了`app(environ, start_response)`， 也就是执行了：

```python
flask_obj = Flask()
flask_obj()
```

当我们调用实例对象的方法时， 其实是执行了类的`__call__`方法。

## 8. Flask 的 _\_call__ 方法

> 所以当前一个请求过来，执行了`WSGIRequestHandler`的`handle`方法，`handle`方法又经过七拐八拐回到了`Flask`的对象中。

```python
class Flask(App):
	def wsgi_app(self， environ: dict， start_response: t.Callable) -> t.Any:
		# 创建请求上下文对象
        ctx = self.request_context(environ)
        error: BaseException | None = None
        try:
            try:
                # 在其中判断是否含有应用上下文，
                ctx.push()
                # 
                response = self.full_dispatch_request()
            except Exception as e:
                error = e
                response = self.handle_exception(e)
            except:  # noqa: B001
                error = sys.exc_info()[1]
                raise
            return response(environ， start_response)
        finally:
            if "werkzeug.debug.preserve_context" in environ:
                environ["werkzeug.debug.preserve_context"](_cv_app.get())
                environ["werkzeug.debug.preserve_context"](_cv_request.get())

            if error is not None and self.should_ignore_error(error):
                error = None

            ctx.pop(error)

    def __call__(self， environ: dict， start_response: t.Callable) -> t.Any:
        return self.wsgi_app(environ， start_response)
```



接下来就按照`self.full_dispatch_request()`的请求进行执行， 

```python
class Flask(App):    
    def dispatch_request():
        """
        	执行视图函数
        """
        req = request_ctx.request
        if req.routing_exception is not None:
            self.raise_routing_exception(req)
        rule: Rule = req.url_rule  # type: ignore[assignment]

        if (
            getattr(rule， "provide_automatic_options"， False)
            and req.method == "OPTIONS"
        ):
            return self.make_default_options_response()
        # otherwise dispatch to the handler for that endpoint
        view_args: dict[str， t.Any] = req.view_args  # type: ignore[assignment]
        # 根据endpoint 去到view_functions 中去匹配路由和视图。并执行。
        return self.ensure_sync(self.view_functions[rule.endpoint])(**view_args)
	
    def preprocess_request(self) -> ft.ResponseReturnValue | None:
        """
        	在请求之前调用，如果有 brfore_request 中间件，则直接调用，如果不返回None， 则直接返  		  回，不走具体的视图方法。
        """
        names = (None， *reversed(request.blueprints))

        for name in names:
            if name in self.url_value_preprocessors:
                for url_func in self.url_value_preprocessors[name]:
                    url_func(request.endpoint， request.view_args)

        for name in names:
            if name in self.before_request_funcs:
                for before_func in self.before_request_funcs[name]:
                    rv = self.ensure_sync(before_func)()

                    if rv is not None:
                        return rv

        return None
    
    def full_dispatch_request(self) -> Response:
        """
        	完整的请求执行流程。
        """
        self._got_first_request = True

        try:
            request_started.send(self， _async_wrapper=self.ensure_sync)
            # 预处理请求， 前置中间件
            rv = self.preprocess_request()
            if rv is None:
                # 执行具体的视图
                rv = self.dispatch_request()
        except Exception as e:
            rv = self.handle_user_exception(e)
        # 对请求进行后置处理
        return self.finalize_request(rv)
```



总的来说，`Flask`的请求逻辑是由`werkzeug`处理`sockrt`底层逻辑， 由`Flask`执行请求视图逻辑。



精简后的代码执行顺序

```python
class Flask(App):    
    
    def run(
        self,
        host: str | None = None,
        port: int | None = None,
        debug: bool | None = None,
        load_dotenv: bool = True,
        **options: t.Any,
    ) -> None:
        """
        1 ===> 服务启动执行 run 方法， 然后调用 werkzeug 的 run_simple 方法。
        """
       
        from werkzeug.serving import run_simple
        run_simple(t.cast(str, host), port, self, **options)
    
    def dispatch_request(self) -> ft.ResponseReturnValue:
        view_args: dict[str, t.Any] = req.view_args  # type: ignore[assignment]
        # ===> 6.6.1 执行 endpoint 对应的视图函数。
        return self.ensure_sync(self.view_functions[rule.endpoint])(**view_args)

    def full_dispatch_request(self) -> Response:
        self._got_first_request = True

        try:
            # ===> 6.5 在请求之前处理一些中间件 brfore_request
            rv = self.preprocess_request()
            if rv is None:
                # ===> 6.6 分发请求。
                rv = self.dispatch_request()
        except Exception as e:
            rv = self.handle_user_exception(e)
        # ===> 6.7 请求结束之后处理相关操作 after_request
        return self.finalize_request(rv)
    
    
    
    def wsgi_app(self, environ: dict, start_response: t.Callable) -> t.Any:
        """
        ===> 6.1 执行具体的请求逻辑。
        """
        # ===> 6.2 构建请求上下文
        ctx = self.request_context(environ)
        try:
            # ===> 6.3 判断是否有应用上下文
            ctx.push()
            # ===> 6.4 执行完整的请求
            response = self.full_dispatch_request()
        except Exception as e:
            error = e
            response = self.handle_exception(e)
        except:  # noqa: B001
            error = sys.exc_info()[1]
            raise
        return response(environ, start_response)


    def __call__(self, environ: dict, start_response: t.Callable) -> t.Any:
        """
        ===> 6 调用 wsgi_app 方法
        """
        return self.wsgi_app(environ, start_response)
```





```python
def make_server(
    host: str,
    port: int,
    app: WSGIApplication,
    threaded: bool = False,
    processes: int = 1,
    request_handler: type[WSGIRequestHandler] | None = None,
    passthrough_errors: bool = False,
    ssl_context: _TSSLContextArg | None = None,
    fd: int | None = None,
) -> BaseWSGIServer:
    # ===> 2.1 创建 wsgi 服务
    return BaseWSGIServer(
        host, port, app, request_handler, passthrough_errors, ssl_context, fd=fd
    )



def run_simple(
    hostname: str,
    port: int,
    application: WSGIApplication,
    use_reloader: bool = False,
    use_debugger: bool = False,
    use_evalex: bool = True,
    extra_files: t.Iterable[str] | None = None,
    exclude_patterns: t.Iterable[str] | None = None,
    reloader_interval: int = 1,
    reloader_type: str = "auto",
    threaded: bool = False,
    processes: int = 1,
    request_handler: type[WSGIRequestHandler] | None = None,
    static_files: dict[str, str | tuple[str, str]] | None = None,
    passthrough_errors: bool = False,
    ssl_context: _TSSLContextArg | None = None,
) -> None:
    # ===> 2 调用 make_server 创建  wsgi 服务，然后等待请求。
    srv = make_server(
        hostname,
        port,
        application,
        threaded,
        processes,
        request_handler,
        passthrough_errors,
        ssl_context,
        fd=fd,
    )
    # ===> 3 调用基类的方法，启动服务，等待请求。
    srv.serve_forever()
```



```python
class BaseServer:
    """
    BaseWSGIServer 类的基类
    """
    def __init__(self, server_address, RequestHandlerClass):
        # 初始化时接收请求处理类：RequestHandlerClass
        self.RequestHandlerClass = RequestHandlerClass
        
    def serve_forever(self, poll_interval=0.5):
        self.__is_shut_down.clear()
        with _ServerSelector() as selector:
            selector.register(self, selectors.EVENT_READ)

            while not self.__shutdown_request:
                ready = selector.select(poll_interval)
                if self.__shutdown_request:
                    break
                if ready:
                    # ===> 4 请求到来执行的方法
                    self._handle_request_noblock()

                self.service_actions()


    def _handle_request_noblock(self):
        try:
            # ===> 4.1 接收 socket 请求
            request, client_address = self.get_request()
        except OSError:
            return
        if self.verify_request(request, client_address):
            # ===> 4.2 处理请求
            self.process_request(request, client_address)

  
    def process_request(self, request, client_address):
        self.finish_request(request, client_address)
        self.shutdown_request(request)

    def finish_request(self, request, client_address):
        # ===> 4.3 初始化 RequestHandlerClass 将 self 作为第三个参数传参
        self.RequestHandlerClass(request, client_address, self)

    def __enter__(self):
        # with 语句返回值
        return self

    def __exit__(self, *args):
        self.server_close()


class TCPServer(BaseServer):
    def __init__(self, server_address, RequestHandlerClass, bind_and_activate=True):
        """Constructor.  May be extended, do not override."""
        BaseServer.__init__(self, server_address, RequestHandlerClass)
        # ===> 2.3 创建 socket 对象，
        self.socket = socket.socket(self.address_family, self.socket_type)
        if bind_and_activate:
            try:
                self.server_bind()
                self.server_activate()
            except:
                self.server_close()
                raise


    def server_activate(self):
        # ===> 2.5 开启 socket 监听
        self.socket.listen(self.request_queue_size)


    def get_request(self):
        # 接受 socket 对象 
        return self.socket.accept()


class HTTPServer(socketserver.TCPServer):
    
    def server_bind(self):
        # ===> 2.4 重写 server_bind 以存储服务器名称。
        socketserver.TCPServer.server_bind(self)
        host, port = self.server_address[:2]
        self.server_name = socket.getfqdn(host)
        self.server_port = port


class BaseWSGIServer(HTTPServer):
    def __init__(
        self,
        host: str,
        port: int,
        app: WSGIApplication,
        handler: type[WSGIRequestHandler] | None = None,
        passthrough_errors: bool = False,
        ssl_context: _TSSLContextArg | None = None,
        fd: int | None = None,
    ) -> None:
        if handler is None:
            # ===> 2.2 将 WSGIRequestHandler 类赋值给 handler 属性
            handler = WSGIRequestHandler

        self.app = app
        
        # ===> 2.3 调用父类的 初始化方法
        super().__init__(
            server_address,
            handler,
            bind_and_activate=False,
        )
    
```



```python
class BaseRequestHandler:
    """
    请求处理类基类
    """
    def __init__(self, request, client_address, server):
        self.request = request
        self.client_address = client_address
        self.server = server
        self.setup()
        try:
            # ===> 5 在初始化时调用 handle 方法
            self.handle()
        finally:
            self.finish()

    def handle(self):
        pass
    

class StreamRequestHandler(BaseRequestHandler):
    ...


class BaseHTTPRequestHandler(socketserver.StreamRequestHandler):
    """
    请求处理基类，该类的注释详细的说明了作者解析HTTP协议的标准。具体课参阅源码。
    """
    def parse_request(self):
        """
        解析 HTTP 请求，得到请求方法 GET 或者 POST 等
        """
        self.command = None  
        command, path = words[:2]
        self.command, self.path = command, path
        return True
    

    def handle_one_request(self):
        """
        处理某一个请求
        """
        try:
            mname = 'do_' + self.command
            method = getattr(self, mname)
            # ===> 5.3 调用 GET 或者 POST 的方法 
            method()
        except TimeoutError as e:
            return

    def handle(self):
        """Handle multiple requests if necessary."""
        self.close_connection = True

        # ===> 5.2 调用请求方法。
        self.handle_one_request()
        while not self.close_connection:
            self.handle_one_request()


class WSGIRequestHandler(BaseHTTPRequestHandler):
    server: BaseWSGIServer
    
    def run_wsgi(self) -> None:
        def execute(app: WSGIApplication) -> None:
            # ===> 5.6 执行 app(environ, start_response)
            application_iter = app(environ, start_response)
            
        try:
            # ===> 5.5 执行 execute 方法 
            execute(self.server.app)
        except (ConnectionError, socket.timeout) as e:
            self.connection_dropped(e, environ)
        except Exception as e:
            if self.server.passthrough_errors:
                raise

            if status_sent is not None and chunk_response:
                self.close_connection = True

            try:
                # if we haven't yet sent the headers but they are set
                # we roll back to be able to set them again.
                if status_sent is None:
                    status_set = None
                    headers_set = None
                execute(InternalServerError())
            except Exception:
                pass

            from .debug.tbtools import DebugTraceback

            msg = DebugTraceback(e).render_traceback_text()
            self.server.log("error", f"Error on request:\n{msg}")

    def handle(self) -> None:
        # ===> 5.1 执行 父类的 handle
        super().handle()


    def __getattr__(self, name: str) -> t.Any:
        if name.startswith("do_"):
            # ===> 5.4 调用 run_wsgi 方法 
            return self.run_wsgi
        return getattr(super(), name)
```





