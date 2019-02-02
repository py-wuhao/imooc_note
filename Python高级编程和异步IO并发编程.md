**魔法方法**

魔法方法不是继承的object，定义了魔法方法解释器会在适当的时候调用

1. \__str__(self)

   print(object) 时调用该方法

2. \__repr__(self)

   object 时调用该方法

3. \__add__(self, x)

   object1 + object2 时调用object1的\__add__()方法

4. \__len__(self)

   len() 内置类型型时效率非常高。举例列表，是元素外置。变量指向第一个元素，并且记录列表长度。len直接取长度

**类和对象**

1. 鸭子类型

   解释：当一只鸟叫起来和走起来等等和鸭子有一样的特征这只鸟可以称为鸭子

   举例：列表的extend方法，只要求参数时可迭代类型。那么列表，集合...都可作为参数

2. 多态

   可以把具有相同方法不同类型的对象赋值给同一个变量。python是动态语言不需要声明变量的类型。在c++中使用多态 父类的指针可以指向子类。

3. 实现了那些魔法方法 就决定了类对象的类型。

4. 抽象基类

   1. 在某些情况下希望判断某个对象的类型

      isinstance(object,type)

   2. 需要强制子类必须实现某些方法

   3. abc模块

      继承 metaclass=abc.ABCMeta

      方法加装饰器 @abc.abstractmethod

   4. 类是模板对象

5. 使用isintance而不是type

   isintance会根据继承关系判断类型，type返回类模板对象地址

6. 类属性和实例属性

   1. 对象共享类属性
   2. 对象.类属性 读取 查找顺序
   3. 对象.类属性 赋值 实际上是动态添加了对象属性

7. mro属性查找顺序 c3算法

   1. 经典类 深度优先广度优先都有缺陷
   2. 新式类c3算法继承顺序

8. 类方法，实例方法，静态方法

9. 私有属性

   __属性名，类外不能直接访问。访问: _当前类名\_\_属性名

10. 对象的自省机制

    定义：通过一定的机制查询到对象的内部结构

    1. \__dict__属性  返回字典
    2. dir(object) 返回列表  详细 没有值

11. super的调用

    并不是调用父类的方法。按mro顺序调用

12. mixin模式

    1. Mixin类功能单一
    2. 不和基类关联，可以和任意基类组合，基类可以不和Mixin关联就能初始化成功
    3. 在Mixin中不要使用super函数

13. with语句

    ```python
    class Sample(object):
        def __enter__(self):
            print('进入')
            return self
    
        def __exit__(self, exc_type, exc_val, exc_tb):
            print('退出')
    
        def do_sample(self):
            print('on sample')
    
    
    with Sample() as s:
        s.do_sample()
    ```

    ```python
    进入
    on sample
    退出
    ```

    

14. contextlib

    简化上下文管理器

    ```python
    import contextlib
    
    
    @contextlib.contextmanager
    def file_open(path):
        print('enter')
        yield {}
        print('exit')
    
    
    with file_open('abc.txt') as f:
        print('hahaha')
    ```

**自定义序列**

1. 序列的分类

   1. 存储数据的类型区分
      1. 容器序列 ：可以存储任意类型的数据
      2. 扁平序列
   2. 是否可变： 创建之后是否可以被修改
      1. 可以序列
      2. 不可变序列 

2. 抽象基类abc的继承关系

3. list中+，+=，extend区别

   1. \+  \__add__()  中调用append()

   2. += \__iadd__() 中调用 extend()

      ```python
          def __iadd__(self, values):
              self.extend(values)
              return self
      ```

      

4. 实现可切片对象

   ```python
   import numbers
   from collections import abc
   
   
   class Group(abc.Sequence):
       def __init__(self, staffs):
           self.staffs = staffs
   
       def __getitem__(self, item):
           cls = type(self)
           if isinstance(item, slice):
               return cls(self.staffs[item])
           elif isinstance(item, numbers.Integral):
               return cls([self.staffs[item]])
   
       def __len__(self):
           return len(self.staffs)
   
   
   staffs = ['zhang', 'li', 'wang']
   group = Group(staffs)
   new_group = group[:2]
   new2_group = group[1]
   pass
   ```

5. bisect维护已排序的序列

   1. 处理已排序的序列 
   2. 维持已排序的序列 （升序）
   3. 二分法查找

6. array

   1. 必须指定类型
   2. 必须存储指定类型
   3. 比列表高效

7. 列表推导式，生成器表达式，字典推导式，集合推导式

   1. 生成器表达式() 并不是元祖tuple，是生成器 generate

8. dict 方法

   1. setdefaule() 

      如果键不在字典中，则默认插入该键

      如在在字典中,则返回该键的值，否则返回默认值

   2. update()

      更新字典

9. defaultdict

   1. 参数传递一个可调用的对象，当键不存在时调用对象作为值，生成键值对

10. 集合的基本操作

11. 字典和集合的实现原理

    1. 存在时对key进行hash计算求出地址值（&0xff）
    2. 发生散列冲突，（策略）重新计算地址值
    3. 取值 对key进行hash计算，取hash值的一部分定位表元，如果表元为空抛出异常，不为空，判断key是否相等，不相等使用hash的另一部分来定位

**对象的引用，可变性和垃圾回收**

1. 变量

   1. 本质上是一个指针，比喻为便利贴
   2. 赋值计算 ，先计算右边，然后变量指向该对象。

2. is和==

   is比较的是对象的id值

   ==比较的是对象的值,魔法方法（\__eq__）

3. 垃圾回收

   通过引用计数，（还有其他机制）

4. 定义类时可变对象作为默认参数的问题

   如果定义类时将可变对象作为默认参数，实例化时没有传参数。实例的属性将指向类的默认参数     cls.\__init\_\_.\_\_defaults__

**元类编程**

1. property

   ```python
   class User:
       def __init__(self):
           self.__age = 100
   
       @property
       def age(self):
           return self.__age
   
       @age.setter
       def age(self, value):
           self.__age = value
   
   
   if __name__ == '__main__':
       user = User()
       print(user.age)
       user.age = 50
       print(user.age)
   ```

2. getattr和getattributr

   1. \_\_getattr\_\_() 在访问对象不存在的属性时调用该方法
   2. \_\_getattribute\_\_() 访问对象属性首先调用该方法

3. setattr(self,x,y)

   self.x = y

4. 属性描述符

   如果user是某个类的实例，那么user.age（以及等价的getattr(user,’age’)）
   首先调用\_\_getattribute\_\_。如果类定义了\_\_getattr\_\_方法，
   那么在\_\_getattribute\_\_抛出 AttributeError 的时候就会调用到\_\_getattr\_\_，
   而对于描述符(\_\_get\_\_）的调用，则是发生在\_\_getattribute\_\_内部的。
   user = User(), 那么user.age 顺序如下：

   （1）如果“age”是出现在User或其基类的\_\_dict\_\_中， 且age是data descriptor， 那么调用其\_\_get\_\_方法, 否则

   （2）如果“age”出现在user的\_\_dict\_\_中， 那么直接返回 obj.\_\_dict\_\_[‘age’]， 否则

   （3）如果“age”出现在User或其基类的\_\_dict\_\_中

   （3.1）如果age是non-data descriptor，那么调用其\_\_get\_\_方法， 否则

   （3.2）返回 \_\_dict\_\_[‘age’]

   （4）如果User有\_\_getattr\_\_方法，调用\_\_getattr\_\_方法，否则

   （5）抛出AttributeError

5. **\_\_new\_\_和\_\_init\_\_**

   1. new 用来控制对象的生成过程，在对象生成之前调用

      如果不返回对象，就不会调用init函数

   2. init 用来完善对象

6. 元类

   1. 元类是创建类的类
   2. python中类的实例化过程，先寻找metaclass，通过metaclass去创建类，如果没有就通过type创建

**迭代器和生成器**

1. 迭代器协议
   1. 迭代器时访问集合内元素的一种方式，一般用来遍历数据
   2. 迭代器和下标访问方式不一样，迭代器不能放回，迭代器提供了一种惰性访问数据方式
   3. 实现了\_\_iter\_\_的对象是可迭代对象
   4. 同时实现\_\_iter\_\_和\_\_next\_\_方法的对象时迭代器

2. 可迭代对象和迭代器例子

   ```python
   from collections.abc import Iterator
   
   
   class Company:
       def __init__(self, staffs):
           self.staffs = staffs
   
       def __iter__(self):
           return MyIterator(self.staffs)
   
   
   class MyIterator(Iterator):
       def __init__(self, staffs):
           self.staffs = staffs
           self.index = 0
   
       def __next__(self):
           try:
               res = self.staffs[self.index]
           except IndexError:
               raise StopIteration
           self.index += 1
           return res
   
   
   company = Company(['a', 'b', 'c'])
   for staff in company.staffs:
       print(staff)
   ```

3. 生成器

   python解释器编译字节码时发现有yield生成 生成器对象。

   打印斐波拉契

   ```python
   def gen_fib(index):
       n, a, b = 0, 0, 1
       while n < index:
           yield b
           a, b = b, a + b
           n += 1
   
   
   for i in gen_fib(10):
       print(i)
   
   ```

4. python如何实现生成器

   栈和堆的区别：

   1. 栈区stack 由编译器自动分配释放，存放函数的参数值，局部变量值等
   2. 堆区heap 一般由程序员分配释放，若不释放，程序结束时由系统回收

   ---

   1. python函数的执行：python.exe 会用一个叫做PyEval_EvalFramEx(c函数)去执行python函数，首先创建一个栈帧
   2. 当python函数调用子函数时，又会创建一个栈帧
   3. 所有栈帧都是分配在堆内存上，这决定了栈帧可以独立于调用者存在

5. yield应用读取大文件

   ```python
   # 读取大文件
   
   def readfile(path, newline):
       with open(path, 'r') as f:
           chunk = ''
           while True:
               while newline in chunk:
                   pos = chunk.index(newline)
                   yield chunk[:pos]
                   chunk = chunk[pos + len(newline):]
               chunk += f.read(4096)
               if newline not in chunk:
                   yield chunk
                   break
   
   
   if __name__ == '__main__':
       for line in readfile('dawenjina.txt', ';'):
           print(line)
   
   
   ```

   

**socket编程**

1. 五层网络协议

   ![1548864814503](C:\Users\gym\AppData\Roaming\Typora\typora-user-images\1548864814503.png)

2. socket 多用户聊天

   ```python
   
   
   ```

3. socket 模拟http请求

   ```python
   
   
   ```

**多任务编程**

1. GIL

   1. GIL global interpreter lock (cpyth)
   2. python中一个线程对应c语言中一个线程
   3. gil使得同一时刻只有一个线程在cup上执行字节码，无法将多个线程映射到多个cpu上执行
   4. gil会根据字节码的执行数以及时间片释放gil,gil遇到io操作时会主动释放

2. 多线程

   1. 主线程会等待子线程结束在结束。可以设置子线程为守护线程，主线线程结束子线程结束。
   2. 多线程实现的两种方式
   3. 导入变量问题
   4. 线程间通信
      1. 共享全局变量 线程不安全
      2. 消息队列Queue  线程安全  Queue使用deque，deque线程安全。

3. 线程间同步

   1. 加锁 Look或者RLook

      1. 加锁会影响性能，获取锁和释放锁都需要时间

      2. 容易造成死锁

         ```python
         A(a、b)
         acquire (a)
         acquire (b)
         
         B(a、b)
         acquire (a)
         acquire (b)
         ```

   2. condition

      1. 用于复杂线程间同步
      2. 启动顺序很重要，应先启动先wait的线程
      3. 在调用with cond之后才能调用wait或者notify方法
      4. condition有两层锁 底层锁会在线程调用了wait方法时释放，上层锁会在每次调用wait的时候分配一把并放入cond的等待队列中，等待notify方法的唤醒
      5. 原理 两层锁结果不太理解

   3. Semaphore

      1. 用于控制进入数量的锁

4. ThreadPoolExecutor 线程池

   1. 主线程中可以获取某一个线程的状态或者某一个任务的状态，以及返回值
   2. 当一个线程完成的时候主线程能立即知道
   3. futures可以让多线程和多进程编码接口一致

5. ThreadPoolExecutor源码分析  （**重点）**

6. 多线程和多进程比较

   1. 耗cpu的操作（计算），用多进程。对于io操作使用多线程。进程切换代价高于线程。

7. 多进程编程

8. 进程间通信

**协程**

1. 基本概念
   1. 并发

      一段时间内，有几个程序在一个cup上轮流执行。

   2. 并行

      同一时刻点有多个程序在多个cpu上执行

   3. 同步 （消息通信的一种机制）

      代码在调用io操作时，必须等待io操作完成才返回的调用方式

   4. 异步

      代码在调用io操作时，不必等待io操作完成就返回的调用方式

   5. 阻塞 （函数调用的一种机制）

      指调用函数的时候当前线程被挂起

   6. 非阻塞

      指调用函数的时候当前线程不会被挂起，而是立即返回

2. Unix下五种IO模型

   1. 阻塞式I/O  

      等待数据返回，顺序执行

      ![image-20180924143108480](https://ws3.sinaimg.cn/large/006tNbRwgy1fvkm86bwwwj31kw0uqtpb.jpg)

   2. 非阻塞I/O

      当下面的代码不依赖上面的返回，是省时的。否则要轮询判断是有数据返回，再执行后面的代码

      ![image-20180924143544454](https://ws4.sinaimg.cn/large/006tNbRwgy1fvkmcvblrfj31kw0tltkg.jpg)

   3. I/O复用

      select 本身是阻塞的，但可以监听多个文件句柄。返回可以使用的句柄。缺点：有将数据从内核拷贝到用户空间的时间消耗

      ![image-20180924145143527](https://ws1.sinaimg.cn/large/006tNbRwgy1fvkmtkiifzj31kw0wwh3n.jpg)

   4. 信号驱动式I/O

      建立一个信号处理程序， 然后操作系统在数据准备好之后，主动发一个信号给我们的信号处理程序，处理程序收到信号之后就会通知应用进程调用`recvfrom`

      ![image-20180924150221575](https://ws4.sinaimg.cn/large/006tNbRwgy1fvkn4jxyr5j31kw0zl194.jpg)

   5. 异步I/O （POSIX的aio_系列函数）

      异步I/O是在将数据复制好之后再通知信号处理程序

      ![image-20180924150618293](https://ws4.sinaimg.cn/large/006tNbRwgy1fvkn8p8vb6j31c40qe10v.jpg)

3. select、poll、epoll

   ![1548988063312](C:\Users\gym\AppData\Roaming\Typora\typora-user-images\1548988063312.png)

   ![1548988162474](C:\Users\gym\AppData\Roaming\Typora\typora-user-images\1548988162474.png)

   ![1548988241427](C:\Users\gym\AppData\Roaming\Typora\typora-user-images\1548988241427.png)

   ![1548988261374](C:\Users\gym\AppData\Roaming\Typora\typora-user-images\1548988261374.png)

4. epoll和select比较

   在高并发的情况下，连接活跃度不是很高，epoll比select好

   并发性不高，同时连接很活跃，select比epoll好

5. 生成器 next、send、close、throw

   1. next :从迭代器返回下一项。如果已耗尽，则引发StopIteration。
   2. send :可以传递值到生成器内部，同时执行到生成器下一项
   3. close :关闭生成器，再次调用生成器会报错
   4. throw :向生成器内部仍一个异常，是上一个yield项位置，并且执行生成器下一项

6. yield from

   1. from itertools import chain  连接多个可迭代对象

   2. yield from 可以将iterable里的值挨个yield出来

   3. ```python
      def g1(gen):
          yield from gen
      
      
      def main():
          g = g1(range(5))
          print(g.send(None))
          print(g.send(None))
          print(g.send(None))
      
      
      if __name__ == '__main__':
          main()
      ```

      main 调用方，  g1：委托生成器， gen：子生成器

      yield from 会在调用方和子生成器之间建立双向通道

7. 理解yield from 

   参考连接 https://juejin.im/post/5b3af9fb51882507d4487144

   （委托生成器为什么要使用while True） 暂时理解为这就是语法吧。

**asyncio编程**

1. 基本例子

   事件循环+回调（驱动生成器）+epoll(IO多路复用)

   ```python
   import asyncio
   import time
   
   
   async def get_html(url):
       print('start html')
       await asyncio.sleep(2)
       print('end html')
   
   if __name__ == '__main__':
       start_time = time.time()
       loop = asyncio.get_event_loop()
       tasks = [get_html('www.baidu.com') for _ in range(10)]
       loop.run_until_complete(asyncio.wait(tasks))
       # loop.run_until_complete(get_html('aa'))
       print(time.time()-start_time)
   
   ```

   

2. 获取协程返回值

   ```python
   import asyncio
   import time
   
   
   async def get_html(url):
       print('start html')
       await asyncio.sleep(2)
       print('end html')
       return 'haha'
   
   
   if __name__ == '__main__':
       start_time = time.time()
       loop = asyncio.get_event_loop()
       # get_future = asyncio.ensure_future(get_html('hhhhhh'))
       # loop.run_until_complete(get_future)
       # print(get_future.result())
       task = loop.create_task(get_html('hhhhhh'))
       loop.run_until_complete(task)
       print(task.result())
       print(time.time() - start_time)
   ```

   ensure_future和create_task区别

   ​	在ensure内部还是调用了 loop.create_task 返回task对象

   ```python
    def ensure_future(coro_or_future, *, loop=None):
           ...
       if loop is None:
               loop = events.get_event_loop()
           task = loop.create_task(coro_or_future)
           if task._source_traceback:
               del task._source_traceback[-1]
           return task
   ```

3. 函数执行完回调函数

   在协程return前 执行了回调函数

   ```python
   import asyncio
   import time
   from functools import partial
   
   
   async def get_html(url):
       print('start html')
       await asyncio.sleep(2)
       print('end html')
       return 'haha'
   
   
   def callback(url, future):  # 注意参数顺序
       print('callback ==='+url)
   
   
   if __name__ == '__main__':
       start_time = time.time()
       loop = asyncio.get_event_loop()
       task = loop.create_task(get_html('hhhhhh'))
       task.add_done_callback(partial(callback, 'qwe'))
       loop.run_until_complete(task)
       print(task.result())
       print(time.time() - start_time)
   
   ```

4. asyncio.wait 和 asyncio.gather区别

   或者更高级，有更丰富的方法

   gather 可以将协程分组

   在python3.6环境 取消任务报错了 concurrent.futures._base.CancelledError

   ```python
   import asyncio
   import time
   
   
   async def get_html(url):
       print('start html')
       await asyncio.sleep(2)
       print('end html')
   
   if __name__ == '__main__':
       start_time = time.time()
       loop = asyncio.get_event_loop()
       group1 = [get_html('www.baidu.com') for _ in range(2)]
       group2 = [get_html('www.baidu.com') for _ in range(2)]
       # loop.run_until_complete(asyncio.wait(tasks))
       group1 = asyncio.gather(*group1)
       group2 = asyncio.gather(*group2)
       group2.cancel()  # 报错了
       loop.run_until_complete(asyncio.gather(group1,group2))
   
       print(time.time()-start_time)
   ```

5. run_until_complete 和 run_forever 区别

   run_util_complete在执行指定协程之后就会停止，而run_forever会一直运行。

   ```python
       def run_until_complete(self, future):
          ...
           if new_task:
               # An exception is raised if the future didn't complete, so there
               # is no need to log the "destroy pending task" message
               future._log_destroy_pending = False
   
           future.add_done_callback(_run_until_complete_cb)
           try:
               self.run_forever()
           except:
               if new_task and future.done() and not future.cancelled():
                   # The coroutine raised a BaseException. Consume the exception
                   # to not log a warning, the caller doesn't have access to the
                   # local task.
                   future.exception()
               raise
               ...
   	def _run_until_complete_cb(fut):
       	exc = fut._exception
       	if (isinstance(exc, BaseException)
       	and not isinstance(exc, Exception)):
           	# Issue #22429: run_forever() already finished, no need to
           	# stop it.
           	return
      	 	fut._loop.stop()
   ```

6. 取消协程任务

   ```python
   import asyncio
   import time
   
   async def get_html(sleep_times):
       print("waiting")
       await asyncio.sleep(sleep_times)
       print("done after {}s".format(sleep_times))
   
   
   if __name__ == "__main__":
       task1 = get_html(2)
       task2 = get_html(3)
       task3 = get_html(3)
   
       tasks = [task1, task2, task3]
   
       loop = asyncio.get_event_loop()
   
       try:
           loop.run_until_complete(asyncio.wait(tasks))
       except KeyboardInterrupt as e:
           all_tasks = asyncio.Task.all_tasks()
           for task in all_tasks:
               print("cancel task")
               print(task.cancel())
           loop.stop()
           loop.run_forever()
       finally:
           loop.close()
   ```

   asyncio.Task.all_tasks() 获取所有的task

   ```python
       @classmethod
       def all_tasks(cls, loop=None):
           """Return a set of all tasks for an event loop.
   
           By default all tasks for the current event loop are returned.
           """
           if loop is None:
               loop = events.get_event_loop()
           return {t for t in cls._all_tasks if t._loop is loop}
   
   ```

   loop.stop() 只是将标记为置位

   ```python
   
       def stop(self):
           """Stop running the event loop.
   
           Every callback already scheduled will still run.  This simply informs
           run_forever to stop looping after a complete iteration.
           """
           self._stopping = True
   ```

   loop.close()释放了资源

   ```python
       def close(self):
           """Close the event loop.
   
           This clears the queues and shuts down the executor,
           but does not wait for the executor to finish.
   
           The event loop must not be running.
           """
           if self.is_running():
               raise RuntimeError("Cannot close a running event loop")
           if self._closed:
               return
           if self._debug:
               logger.debug("Close %r", self)
           self._closed = True
           self._ready.clear()
           self._scheduled.clear()
           executor = self._default_executor
           if executor is not None:
               self._default_executor = None
               executor.shutdown(wait=False)
               
   ```

7. 在协程中插入协程

   ```python
   import asyncio
   
   async def compute(x, y):
       print("Compute %s + %s ..." % (x, y))
       await asyncio.sleep(1.0)
       return x + y
   
   async def print_sum(x, y):
       result = await compute(x, y)
       print("%s + %s = %s" % (x, y, result))
   
   loop = asyncio.get_event_loop()
   loop.run_until_complete(print_sum(1, 2))
   loop.close()
   ```

   ![image-20180927145609440](http://ww1.sinaimg.cn/large/c518973bgy1fvo3tan7ccj21n00siqnt.jpg)

   首先event loop为print_sum()创建Task，通过Task驱动print_sum()运行，print_sum()调用了子协程，自身暂停了。print_sum就相当于委托生成器，那么Task和compute间建立了双向通道，compute暂停1s直接返回到Task，Task告诉event loop暂停1s，1s后让compute继续执行，computer() return后抛出异常会被print_sum捕获，print_sum提取返回值继续执行。print_sum执行完成同样会抛出异常被Task处理，再返回给event_loop.

8. call_soon

   call_coon 插入一个函数在下即可执行。实际上调用call_later 0s后执行

   ```python
   import asyncio
   
   
   def callback(sleep_times):
       print("success time {}".format(sleep_times))
   
   
   def stoploop(loop):
       loop.stop()
   
   
   if __name__ == "__main__":
       loop = asyncio.get_event_loop()
       loop.call_soon(callback, 4)
       loop.call_soon(stoploop, loop)
       loop.run_forever()
   ```

   ```python
       def call_soon(self, callback, *args):
           return self.call_later(0, callback, *args)
   
   ```

9. call_later

   指定多少秒后执行函数。

10. call_at

    指定时间相对于loop内部的时间