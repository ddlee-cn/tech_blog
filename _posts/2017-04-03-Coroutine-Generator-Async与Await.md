---
title: 'Coroutine,Generator,Async与Await'
layout: post
categories: [Python]
---


#### Generator

Generator能保存自己的状态，进入一种“Paused”状态，再次调用时会继续执行。

Generator的好处之一是节省了存储空间开销，带一些”流处理”的思想。

其实，我们也可以对Generator进行传入数据的操作：

```python
def coro():
    hello = yield "Hello"
    yield hello

c = coro()
print(next(c))
print(c.send("World"))
```

#### Coroutine
coroutine可以认为是generator思想的泛化：
- generator一个一个地吐出数据（返回值）
- coroutine一个一个地吃掉数据（传入参数）并返回结果，即可控地执行函数

关键点在于，generator与coroutine都能保存自己的状态，而这种特点正可以用于任务切换。yield可以看做是操作系统在进行进程管理时的traps:

![traps]({{ site.baseurl }}/user-imgs/coroutine/os.png)

实际上，coroutine可以看做”用户自定义”的进程，状态、启用和暂停都可控，David Beazley就利用这一点用coroutine实现了Python上的操作系统（参见Reference)。


#### Conroutine与Concurrent Programming

Concurrent Programming中有Task的概念，有如下特点：
- 独立的控制流
- 内部状态变量
- 支持计划任务（暂停、恢复执行）
- 与其他Task通信

```python
@coroutine
def grep(pattern):  #正则匹配
    print "Looking for %s" % pattern
    while True:
        line = (yield)
        if pattern in line:
            print line,
```
conroutine有自己的控制流（while/if），有局部变量（pattern, line），能暂停和恢复（yield()/send()），能相互通信（send()）

====》coroutine就是一种Task！

Python Docs中提供了一个例子：
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

执行方式如下图：

![Chaining coroutines]({{ site.baseurl }}/user-imgs/coroutine/tulip_coro.png)

利用coroutine，可以在一个线程(Task)上实现异步。

#### Impletation

coroutine有两种实现方式，基于generator和原生async, awati关键字。

##### generator based coroutine
```python
import asyncio
import datetime
import random

@asyncio.coroutine
def display_date(num, loop):
    end_time = loop.time() + 50.0
    while True:
        print("Loop: {} Time: {}".format(num, datetime.datetime.now()))
        if (loop.time() + 1.0) >= end_time:
            break
        yield from asyncio.sleep(random.randint(0, 5))

loop = asyncio.get_event_loop()

asyncio.ensure_future(display_date(1, loop))
asyncio.ensure_future(display_date(2, loop))

loop.run_forever()
```
上面的程序实现了在同一个线程里交互执行两个函数（sleep），而又能保持各自的状态


##### Native support(python 3.5+)

只需要修改函数定义头和`yield from`为关键字`await`即可。
```python
async def display_date(num, loop, ):
    end_time = loop.time() + 50.0
    while True:
        print("Loop: {} Time: {}".format(num, datetime.datetime.now()))
        if (loop.time() + 1.0) >= end_time:
            break
        await asyncio.sleep(random.randint(0, 5))
```

#### 拾遗
Coroutine常翻译成“协程”。

#### Reference:
- [David Beazley @ PyCon2009 Slides](http://www.dabeaz.com/coroutines/Coroutines.pdf)
- [PYTHON: GENERATORS, COROUTINES, NATIVE COROUTINES AND ASYNC/AWAIT](http://masnun.com/2015/11/13/python-generators-coroutines-native-coroutines-and-async-await.html)
- [Python 3.6 Docs: Taks and coroutines](https://docs.python.org/3/library/asyncio-task.html)


@[ddlee](https://ddlee.cc)
