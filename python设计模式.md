# python-design-patterns

### 赞成对象组合而不是继承

```
class User(DbObject):
    pass
```

可以这样做

```
class User:
    _persist_methods = ['get', 'save', 'delete']

    def __init__(self, persister):
        self._persister = persister

    def __getattr__(self, attribute):
        if attribute in self._persist_methods:
            return getattr(self._persister, attribute)
```

优势，可以限制包装类那些方法公开。可以在运行时注入persister实例。

### 行为模式

#### 责任链模式

```python
class ContentFilter(object):
    def __init__(self, filters=None):
        self._filters = list()
        if filters is not None:
            self._filters += filters

    def filter(self, content):
        for filter in self._filters:
            content = filter(content)
        return content

filter = ContentFilter([
                offensive_filter,
                ads_filter,
                porno_video_filter])
filtered_content = filter.filter(content)
```

例如，我们想过滤一些内容，我们可以实现不同的过滤器，每个过滤器执行一种精确定义的过滤类型。

#### 命令模式

```python
class RenameFileCommand(object):
    def __init__(self, from_name, to_name):
        self._from = from_name
        self._to = to_name

    def execute(self):
        os.rename(self._from, self._to)

    def undo(self):
        os.rename(self._to, self._from)

class History(object):
    def __init__(self):
        self._commands = list()

    def execute(self, command):
        self._commands.append(command)
        command.execute()

    def undo(self):
        self._commands.pop().undo()

history = History()
history.execute(RenameFileCommand('docs/cv.doc', 'docs/cv-en.doc'))
history.execute(RenameFileCommand('docs/cv1.doc', 'docs/cv-bg.doc'))
history.undo()
history.undo()
```

#### 单例模式

```python
class Logger(object):
    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, '_logger'):
            cls._logger = super(Logger, cls
                    ).__new__(cls, *args, **kwargs)
        return cls._logger
```

### 结构模式

#### 适配器模式

假设要给目标发送指定格式的信息，使用write()方法(每个文件对象都有)

```python
upd_logger = SocketWriter('1.2.3.4', '9999')
log('Something happened', upd_logger)
```

但是udp套接字没有write()方法

```python
import socket

class SocketWriter(object):

    def __init__(self, ip, port):
        self._socket = socket.socket(socket.AF_INET,
                                     socket.SOCK_DGRAM)
        self._ip = ip
        self._port = port

    def write(self, message):
        self._socket.send(message, (self._ip, self._port))

def log(message, destination):
    destination.write('[{}] - {}'.format(datetime.now(), message))

upd_logger = SocketWriter('1.2.3.4', '9999')
log('Something happened', upd_logger)
```

https://www.toptal.com/python/python-design-patterns

