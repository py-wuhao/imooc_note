# Tornado从入门到进阶 打造支持高并发的技术论坛

### 前言

tornado 为解决高并发和长连接设计的

### mac下搭建开发环境

https://www.imooc.com/article/25365

### django、flask、tornado比较

1. django 大而全
2. flask 小而精
3. tornado 
   1. 基于协程的，
   2. 高并发，
   3. 不仅是web框架还是web服务器
   4. 支持websocket长连接

#### 本质区别

1. tornado、gevent、asyncio、aiohttp：底层使用事假循环+协程
2. django和flask：传统模型，阻塞io模型。每个请求独占一个线程。

#### WSGI、wsgi、uwsgi、uWSGI、nginx

1. WSGI：是web server 和 web application间的通信协议。

   1. WSGI server负责从客户端接收请求，将request转发application，将application返回的response返回给客户端；
   2. WSGI application接收由server转发的request，处理请求，并将处理结果返回给server。application中可以包括多个栈式的中间件(middlewares)，这些中间件需要同时实现server与application，因此可以在WSGI服务器与WSGI应用之间起调节作用：对服务器来说，中间件扮演应用程序，对应用程序来说，中间件扮演服务器。

   WSGI协议其实是定义了一种server与application解耦的规范，即可以有多个实现WSGI server的服务器，也可以有多个实现WSGI application的框架，那么就可以选择任意的server和application组合实现自己的web应用。例如uWSGI和Gunicorn都是实现了WSGI server协议的服务器，Django，Flask是实现了WSGI application协议的web框架，可以根据项目实际情况搭配使用。

2. uwsgi：是uWSGI服务器的独占协议，用于定义传输信息的类型。（不太理解）

3. uWSGI：是web服务器，实现了WSGI协议、uwsgi协议、http协议等。

4. nginx：是一个反向代理服务器。

   1. 正向代理：比如vpn，浏览器主动请求代理服务器，代理服务器转发请求到对应的目标服务器。代理了用户。
   2. 反向代理，部署在web服务器上，代理外部网络对内部网络的访问。浏览器访问服务器，必须经过各种代理。也就是代理了服务器。

   反向代理的作用

   1. 安全，客户端对web服务器的访问要先经过反向代理服务器，这样可以防止外服程序对web服务器的直接攻击。
   2. 负载均衡，反向代理服务器可以根据Web服务器的负载情况，动态地把HTTP请求交给不同的Web服务器来处理，前提是要有多个Web服务器。
   3. 提升Web服务器的IO性能。一个HTTP请求的数据，从客户端传输给服务器，是需要时间的，例如N秒，如果直接传给Web服务器，Web服务器就需要让一个进程阻塞N秒，来接收IO，这样会降低Web服务器的性能。如果使用反向代理服务器，先让反向代理服务器接收完整个HTTP请求，再把请求发给Web服务器，就能提升Web服务器的性能。还有一些静态文件的请求，可以直接交给反向代理来处理，不需要经过Web服务器。

### 简单httpclient测试

1. ```python
   from tornado import httpclient
   import tornado
   
   
   async def f():
       http_client = httpclient.AsyncHTTPClient()
       try:
           response = await http_client.fetch("http://www.baidu.com")
       except Exception as e:
           print("Error: %s" % e)
       else:
           print(response.body.decode())
   
   
   if __name__ == '__main__':
       io_loop = tornado.ioloop.IOLoop.current()
       io_loop.run_sync(f)
   ```

   current() : 获取全局事件循环，如果没有就创建。单例模式

   run_sync(f): 运行完f协程后停止事件循环



### url配置

```python
import tornado
from tornado import web


class IndexHandler(web.RequestHandler):
    async def get(self, *args, **kwargs):
        self.write('hello1')


class PeopleNameHandler(web.RequestHandler):
    def initialize(self, name):
        print(name)

    async def get(self, name, *args, **kwargs):
        self.write('hello1:{}'.format(name))


class PeopleAgeHandler(web.RequestHandler):
    async def get(self, age, *args, **kwargs):
        self.write(age)


class PeopleInfoHandler(web.RequestHandler):
    async def get(self, name, age, *args, **kwargs):
        self.write(name + age)


urls = [
    web.URLSpec('/', IndexHandler, name='index'),
    web.URLSpec('/people/(\d+)/?', PeopleAgeHandler),
    web.URLSpec('/people/(\w+)/?',  PeopleNameHandler,{'name': 'wh'}),
    web.URLSpec('/people/(?P<name>\w+)/(?P<age>\d+)/?', PeopleInfoHandler),

]

if __name__ == '__main__':
    app = web.Application(urls,
                          debug=True,
                          )
    app.listen(8888)
    tornado.ioloop.IOLoop.current().start()


```

1. Application() 组成一个web应用程序的请求集合
2. URLSpec() 指定url和处理程序之间的映射

### options

1. ```python
   import tornado
   from tornado import web
   from tornado.options import options, define
   
   define('port', default=8888, help='在指定端口运行', type=int)
   options.parse_command_line()
   # options.parse_config_file('conf.cfg')
   
   class IndexHandler(web.RequestHandler):
       async def get(self, *args, **kwargs):
           self.write('hello1')
   
   
   urls = [
       web.URLSpec('/', IndexHandler, name='index'),
   
   ]
   
   if __name__ == '__main__':
       app = web.Application(urls,
                             debug=True,
                             )
       app.listen(options.port)
       tornado.ioloop.IOLoop.current().start()
   
   ```

2. define() : 定义一些可以在命令行中传递的参数以及类型

3. options 是一个类，全局只有一个options

4. 使用命令行参数 options.参数名 ，通过\__getattr__()魔法方法实现

### RequestHandler常用方法

详细查看文档 ： https://tornado-zh.readthedocs.io/zh/latest/web.html#

#### 入口 entry points

1. RequestHandler.initialize()	实例化对象时调用
2. RequestHandler.prepare()  每个请求开始请调用
3. RequestHandler.on_finish() 在一个请求结束后被调用

#### http方法

1. RequestHandler.get(*args, **kwargs)
2. RequestHandler.head(*args, **kwargs)
3. RequestHandler.post(*args, **kwargs)
4. RequestHandler.delete(*args, **kwargs)
5. RequestHandler.patch(*args, **kwargs)
6. RequestHandler.put(*args, **kwargs)
7. RequestHandler.options(*args, **kwargs)

#### Input

1. `RequestHandler.get_argument`**(***name***,** *default=[]***,** *strip=True***)** 返回name参数的值，如果有多个返回最后一个。查找查询字符串和请求体。
2. `RequestHandler.get_arguments`**(***name***,** *strip=True***)**：返回name参数列表，如果不存在返回空列表
3. `RequestHandler.get_query_argument`**(***name***,** *default=[]***,** *strip=True***)**：从查询字符串中返回给定的name参数，如果没有指定参数也没有默认值会抛出异常，如果有多个返回最后一个
4. `RequestHandler.get_query_arguments`**(***name***,** *strip=True***)**：同上返回列表
5. `RequestHandler.get_body_argument`**(***name***,** *default=[]***,** *strip=True***)**：同上，从请求体中查找
6. `RequestHandler.get_body_arguments`**(***name***,** *strip=True***)**：同上返回列表

#### Output

1. `RequestHandler.set_status`**(***status_code***,** *reason=None***)**：设置响应状态码
2. `RequestHandler.write`**(***chunk***)**：把给定块写到输出buffer.
3. `RequestHandler.flush`**(***include_footers=False***,** *callback=None***)**：将当前输出缓冲区写到网络
4. `RequestHandler.finish`**(***chunk=None***)**：完成响应, 结束HTTP 请求.
5. `RequestHandler.redirect`**(***url***,** *permanent=False***,** *status=None***)**：重定向到给定的URL(可以选择相对路径).301永久，302临时
6. `RequestHandler.render`**(***template_name***,** ***kwargs***)**：使用给定参数渲染模板并作为响应.

#### RequestHandler子类

1. StaticFileHandler
2. RedirectHandler

### 模板

### UIModule

### 留言板

```python
from tornado import web
import tornado
from aiomysql import create_pool


class IndexHandler(web.RequestHandler):
    def initialize(self, db):
        self.db = db

    async def get(self, *args, **kwargs):
        id = ''
        name = ''
        email = ''
        address = ''
        message = ''
        async with create_pool(host=self.db['host'], port=self.db["port"],
                               user=self.db["user"], password=self.db["password"],
                               db=self.db["name"], charset="utf8") as pool:
            async with pool.acquire() as conn:
                async with conn.cursor() as cur:
                    await cur.execute("SELECT id, name, email, address, message from tb_message")
                    try:
                        id, name, email, address, message = await cur.fetchone()
                    except Exception as e:
                        pass
        self.render("message.html", id=id, email=email, name=name, address=address, message=message)

    async def post(self, *args, **kwargs):
        id = self.get_body_argument('id', '')
        name = self.get_body_argument('name', '')
        email = self.get_body_argument('email', '')
        address = self.get_body_argument('address', '')
        message = self.get_body_argument('message', '')

        async with create_pool(host=self.db['host'], port=self.db["port"],
                               user=self.db["user"], password=self.db["password"],
                               db=self.db["name"], charset="utf8") as pool:
            async with pool.acquire() as conn:
                async with conn.cursor() as cur:
                    if not id:
                        await cur.execute("INSERT INTO tb_message(name, email, address, message) VALUES('{}','{}','{}','{}')".format(name,email,address,message))
                    else:
                        await cur.execute(
                            "update tb_message set name='{}', email='{}', address='{}', message='{}'".format(name, email,
                                                                                                          address,
                                                                                                          message))
                    await conn.commit()
        # self.render("message.html", id=id, email=email, name=name, address=address, message=message)
        self.redirect(self.reverse_url('index'))



settings = {
    'static_path': 'static',
    'static_url_prefix': '/static/',
    'template_path': 'templates',
    "db": {
        "host": "106.12.104.43",
        "user": "root",
        "password": "mysql",
        "name": "message",
        "port": 3306
    }

}
if __name__ == '__main__':
    app = web.Application([
        web.URLSpec('/', IndexHandler, {'db': settings['db']}, name='index'),
    ], debug=True, **settings)
    app.listen(8888)
    tornado.ioloop.IOLoop.current().start()

```

html中禁止转义 {% autoescape None %}

### RESTful API 设计指南

http://www.ruanyifeng.com/blog/2014/05/restful_api.html

### 前后端分离之JWT用户认证

https://www.jianshu.com/p/180a870a308a

