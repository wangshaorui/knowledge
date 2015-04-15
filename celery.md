#Celery

标签（空格分隔）： celery

---


Celery是一个分布式任务队列工具，是一个异步的任务队列基于分布式消息传递。参考[官网](http://www.celeryproject.org/)。  
##1. 基础概念
- Broker，简单说就是"消息队列"，Celery的基本工作是管理分配任务到不同的服务器，至于服务器之间的通信则是交给了如“RabbitMQ” 第三方服务。
- Task，任务，在Celery中，每个Python function就是一个task，只要在function前面修饰”@task()“，Celery就知道这是一个task，需要异步调用task的时候，task.delay()就可以了，当然有更复杂的调用函数，task.apply_async()，这里面可以指定啥时候调用什么的，这里的调用就是将task加入到queue中，而queue是保存在指定的Broker中的。
- Worker，Celery将你要异步处理的task加入到一个queue中，然后空闲的Worker就会将queue中的task给取走交给服务器。


##2. 任务调用  

任务(task)调用有三个API: 

    #给任务发送消息
    apply_async(args[,kwargs[, ...]])
    
    #给任务发送消息的简单版，但是不支持execution options(apply_async有三个部分的参数，第一部分就是task里面的python function的参数，比如add(x,y)的x,y，第二个参数叫作keyword arguments，就是设定一些环境变量，第三个参数就是execution options，也就是这个task本身的执行选项，时间啊之类)
    delay(*args, **kwargs)
    
    #类似直接调用的意思，即不是让worker来执行任务，而是当前的进程来执行。
    calling(__call__)

关于execution options有这些主要的参数：
###Link(callbacks/errbacks)
就是一个任务接着一个，回调任务作为一个partial argument在父任务完成的时候被调用。

    add.apply_async((2, 2), link=add.s(16))

即(2 + 2) + 16 = 20

###ETA and countdown
eta必须是一个datatime对象。你可以设定一个eta，可以让你的任务在这个eta开始。  
countdown是一个整型，它是eta的简版，以秒为单位。  
这两个都保证任务会在这个时间后执行，但是这个时间无法非常确定，因为各方面的原因（网络延时，任务队列太繁忙）。

###Expiration
expires参数定义了一个可选的到期时间，可以以秒为单位，也可以以datetime。

###序列化
数据在客户端于worker之间的传递需要序列化。
Celery内建的支持 pickle,json,yaml和msgpack方式序列化，当然也可以自定义序列化方式（要在Kombu中注册）。
如果要指定某种内建的方式：

    >>> add.apply_async((10, 10), serializer='json')
    
###压缩
Celery支持gzip和bzip2，当然你也可以自定义压缩的方式(要在kombu中注册)。
指定某种方式的话：

    >>> add.apply_async((2, 2), compression='zlib')
    
##3. Canvas:Designing Workflows
有时候，你需要把一个调用函数的某些信息传递到一个进程或者做为参数传递到另外一个函数的时候，Celery用一种叫subtasks完成这种任务。如：  

    >>> add.subtask((2,2), countdown=10)
    tasks.add(2,2)
简写方式：  

    >>> add.s(2,2)
    tasks.add(2,2)
    
subtasks的实例也支持调用API，也就是delay和apply_async方法。
但对于subtasks也有一些不同，看例子：

    >>> s1 = add.s(2, 2)
    >>> res = s1.delay()
    >>> res.get()
    4
add任务接受两个参数，这个subtasks指定了两个参数，它就完成了一个complete signature。  

但是这样：  

    # incomplete partial: add(?, 2)
    >>> s2 = add.s(2)

这种incomplete signatures称之为partials。
这个时候，s2需要另外一个参数才能完成运算，而再次调用的时候，就可以完成了，像这样：

    # resolves the partial: add(8, 2)
    >>> res = s2.delay(8)
    >>> res.get()
    10
    
###The Primitives（原语？）
The primitives are subtasks themselves, so that they can be combined in any number of ways to compose complex workflows.
####Groups
一个group调用了一系列parallel的任务，看例子：

    >>> from celery import group
    >>> from proj.tasks import add
    >>> group(add.s(i, i) for i in xrange(10))().get()
    [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
    
Partial group：

    >>> g = group(add.s(i) for i in xrange(10))
    >>> g(10).get()
    [10, 11, 12, 13, 14, 15, 16, 17, 18, 19]

add.s(i)这个就是partial，只有一个参数，所以在g(10)中传递10这个参数之后，每个add.s(i,10)，就make a complete signature了。
    
####Chains
任务是可以被链接的，也就是一个任务完成后，就把结果返回给另外一个任务，像这样：

    >>> from celery import chain
    >>> from proj.tasks import add, mul
    # (4 + 4) * 8
    >>> chain(add.s(4, 4) | mul.s(8))().get()
    64

还有partial chain:

    # (? + 4) * 8
    >>> g = chain(add.s(4) | mul.s(8))
    >>> g(4).get()
    64

还有这样的写法也是可以的：

    >>> (add.s(4, 4) | mul.s(8))().get()
    64

####Chords
一个chord就是group带有一个回调：

    >>> from celery import chord
    >>> from proj.tasks import add, xsum
    >>> chord((add.s(i, i) for i in xrange(10)), xsum.s())().get()
    90
    
一个group通过chain另一个任务，就会自动变成一个chord:

    >>> (group(add.s(i, i) for i in xrange(10)) | xsum.s())().get()
    90
    
由于这些primitives都是subtask类型，所以可以任意组合成你想要的样子，比如：

    >>> upload_document.s(file) | group(apply_filter.s() for filter in filters)
    
###Signatures
signature()用一种能传递给一个函数的方式，包含了一个任务调用的 arguments(参数，即任务本身的参数，像add(x,y)中的参数), keyword arguments(关键字参数，就是debug=false,true这类参数), and execution options(执行选项，比如运行时间countdown，到期时间expirt)。

signatures通常也被叫作"subtasks"。可以这样用：

    >>> from celery import signature
    >>> signature('tasks.add', args=(2, 2), countdown=10)
    tasks.add(2, 2)

或者你直接使用task的subtask方法：

    >>> add.subtask((2, 2), countdown=10)
    tasks.add(2, 2)

简写版就是这样：

    >>> add.s(2, 2)
    tasks.add(2, 2)

###Immutabel signatures
意思就是partial的任务可以在回调的时候，把参数值再传进来，但有时候并不想得到某个函数的值，这个时候就可以把这个函数的immutable设成true：

    >>> add.subtask((2, 2), immutable=True)
    
也可以简写：

    >>> add.si(2, 2)
    
看例子：

    >>> res = (add.si(2, 2) | add.si(4, 4) | add.s(8, 8))()
    >>> res.get()
    16
    >>> res.parent.get()
    8
    >>> res.parent.parent.get()
    4


