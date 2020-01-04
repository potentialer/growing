# __ new __

 __ new __ (cls[,...])的参数，__ new __ 方法的第一个参数是这个类，而其余的参数会在调用成功后全部传递给 __ init __ 方法初始化。

所以， __ new __ 方法（第一个执行）先于 __ init __ 方法执行：

我们比较两个方法的参数，可以发现__new__方法是传入类(cls)，而__init__方法传入类的实例化对象(self)，而有意思的是，__ new __ 方法返回的值就是一个实例化对象（ps:如果__new__方法返回None，则__init__方法不会被执行，并且返回值只能调用父类中的__new__方法，而不能调用毫无关系的类的__new__方法）。

我们可以这么理解它们之间的关系，__new__是开辟疆域的大将军，而__init__是在这片疆域上辛勤劳作的小老百姓，只有__new__执行完后，开辟好疆域后，__init__才能工作。

```python
class Base:
    def __init__(self):
        print('这是初始化方法里面')
    def __new__(cls, *args, **kwargs):
        print('这个cls是：',cls)  # cls 就是Base类
        print('这是在new方法里面')
        return object.__new__(cls)  # 必须有返回值
#实例的时候会先调用_new_方法，然后再调用初始化方法
test = Base()
```

绝大多数情况下，我们都不需要自己重写__new__方法，但在当继承一个不可变的类型（例如str类,int类等）时，它的特性就尤显重要了。我们举下面这个例子：

INIT

```python
class CapStr(str):
    def __init__(self,string):
        self = string.upper()
 
a = CapStr("I love China!")
print(a)
```

NEW

```python
class CapStr(str):
    def __new__(cls, string):
        string = string.upper()
        return super().__new__(cls, string)
```

我们可以根据上面的理论可以这样分析，我们知道字符串是不可改变的，所以第一个例子中，传入的字符串相当于已经被打下的疆域，而这块疆域除了将军其他谁也无法改变，__init__只能在这块领地上干瞪眼，此时这块疆域就是”I love China!“。而第二个例子中，__new__大将军重新去开辟了一块疆域，所以疆域上的内容也发生了变化，此时这块疆域变成了”I LOVE CHINA!“。

小结：__new__和__init__想配合才是python中真正的类构造器。

# 单例模式

**单例模式（Singleton Pattern）**是一种常用的软件设计模式，该模式的主要目的是确保**某一个类只有一个实例存在**。当你希望在整个系统中，某个类只能出现一个实例时，单例对象就能派上用场。

比如，某个服务器程序的配置信息存放在一个文件中，客户端通过一个 AppConfig 的类来读取配置文件的信息。如果在程序运行期间，有很多地方都需要使用配置文件的内容，也就是说，很多地方都需要创建 AppConfig 对象的实例，这就导致系统中存在多个 AppConfig 的实例对象，而这样会严重浪费内存资源，尤其是在配置文件内容很多的情况下。事实上，类似 AppConfig 这样的类，我们希望在程序运行期间只存在一个实例对象。

在 Python 中，我们可以用多种方法来实现单例模式

```python
class Earth:
    def __new__(cls):
        if not hasattr(cls, 'instance'):
            cls.instance = super().__new__(cls)
            return cls.instance
    def __init__(self):
        self.name = 'earth'

e = Earth()
print(e, id(e))
print(e.name)
a = Earth() #  None
print(a, id(a))
print(a.name)
```



# 定制属性

```python
# 查
print(hasattr(a,'name'))
print(getattr(a,'name'))
print(a.__getattribute__('name'))
# 改
setattr(a, 'name', 6)
a .__setattr__('name', 5)
print(getattr(a,'name'))#  查
# 增
a.aaa = 1
setattr(a, 'bbb', 2)  # 有bbb属性就改，没有就增
a.__setattr__('ccc', 3) # 同上
print(getattr(a,'ccc'))#  查
# 删
delattr(a, 'ccc')
a.__delattr__('bbb')
print(getattr(a,'ccc'))#  查
del a

def __getattr__(self, item):
    print('No This Attribute')
    

getattr(re,'aaa') 
   
aaa属性不存在时，如果定义了此方法，则调用方法
```

# 描述符

```python
class MyClass:
    def __get__(self, instance, owner):
        print('获得S级武器')

    def __set__(self, instance, value):
        print('This is %s' % value)

    def __delete__(self, instance):
        print('武器已经损坏')


class Control:
    attr = MyClass()  # 类的实例化，类属性


c = Control()
c.attr  # 调用属性，回去我们MyClass类里面执行__get__方法
c.attr = 10  # 重新赋值  调用__set__方法
del (c.attr)# 调用__delete__方法

```

# 闭包复习

```python
# 装饰器
# 闭包的应用
# 写函数
def outer():
    a = 2

    def inner():
        print(a)

    return inner# 返回来的是函数体


x = outer# 函数体
a = x()# 返回值，对象  a = inner
a()# inner()调用
```

# 装饰器

概念：不改变原有函数的基础上，给函数增加一些新的功能

​	不会，不影响之后写代码

​	会，轻松很多，节省代码量

```python
def func():
    print('__正在登陆__')# 登陆功能，不改变增加验证功能
```

闭包中可以传入函数吗

```python
def outer(func):# func-参数  ，函数体
    a = 2

    def inner():
        print('------验证功能------')
        func()

    return inner# 返回来的是函数体

@outer # a = outer(func) # 最后引出
def func():
    print('------正在登陆-----')# 登陆功能，不改变增加验证功能       
 
# 把函数传入闭包中，在闭包里边调用登陆函数
x = outer# 函数体
a = x(func)# 传入函数体
a()# inner()调用{func()调用}
>>>a = outer(func)
>>>a()

```

装饰器用来完成一些新的功能添加到新的

```python
@outer
def f2():
    print('---退出功能----')
```

类也可以使用

内置装饰器

```python
class Rectangles:
    name = 'bbbb'
    def __init__(self, length, width):
        self.length = length
        self.width = width

    def area(self):
        areas = self.length * self.width
        return areas

    @property  # 就像访问属性一样  调用可以不用括号
    def areas(self):
        return self.width * self.length

    @staticmethod  # 静态方法  和class类断开联系  不加self
    def func():  # self  在调用的时候会报错
        print('staticmethod func')

    @classmethod  # 类方法 传递类本身  直接类方法
    def show(cls):  # cls 代表类本身
        print(cls.name)
        print('show fun')
a1 = Rectangles(20,30)
print(a1.area())
print(a1.areas)# 装饰器 property
Rectangles.func()# 不用实例化，不能使用类属性和方法，类中的函数
Rectangles.show()# 不用实例化，可以使用类属性和方法

```

类当做装饰器必须使用 __ call __

```python
class Test_Class:
    def __init__(self, func):
        self.func = func
    def __call__(self,*args,**kwargs):
        print('类----cal----')
        return self.func()
@Test_Class
def fun_test():
    print('这是个测试函数')
fun_test()

```

代码运行时间案例

```python
import time

def run_time(func):
    def new_fun(*args,**kwargs):
        t0 = time.time()
        back = func(*args,**kwargs)
        print('函数运行的时间: %s'%(time.time() - t0))
        return back
    return new_fun

@run_time
def aaa():
    time.sleep(5)
aaa()
```











