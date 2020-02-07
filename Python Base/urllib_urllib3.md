编写爬虫首先就是选择一个好用的网络库，通过网络库的API发送请求并接收服务端的响应。

# `urllib`

### 1. `urllib` 简介

`python3` 中内置HTTP请求库，不需要单独安装。

### 1. 1 四个模块

- request : 最基本的HTTP请求库，可以发送HTTP请求，并接收服务端的响应数据。
- parse : 工具模块，提供了很多处理URL的API，如拆分、解析、合并等。
- error : 异常处理模块，如果出现请求错误，可以捕获这些异常，然后根据实际情况，进行重试或者直接忽略或进行其他操作。
- `robotparse` : 主要用来识别网站的robots.txt 文件，然后判断哪些网站可以抓取，哪些网站不可以抓取

#### `urllib.urlopen`

`urlopen` 是一个简单发送网络请求的方法。它接收一个字符串格式的url，它会向传入的url发送网络请求，然后返回结果。

```python
from urllib import request
response = request.urlopen(url="http://httpbin.org/get") # 测试爬虫请求头信息
```

`urlopen` 默认会发送get请求，当传入data参数时，则会发起POST请求。data参数是字节类型、者类文件对象或可迭代对象。

```python
response = request.urlopen(url="http://httpbin.org/post",data=b'username=alice&password=123456')
```

还可以设置超时，如果请求超过设置时间，则抛出异常。timeout没有指定则用系统默认设置，timeout只对， `https` 以及 `ftp` 连接起作用。它以秒为单位，比如可以设置timeout=0.1 超时时间为0.1秒。

```python
response = request.urlopen(url='http://www.baidu.com',timout=0.1)
```

#### `urllib.request`

request模块主要负责构造和发起网络请求，并在其中添加Headers，Proxy等。利用它可以模拟浏览器的请求发起过程。

1. 发起网络请求
2. 添加Headers
3. 操作cookie
4. 使用代理

Request对象来构建更加完整的请求。

```python
req = request.Request('http://www.baidu.com')
response = request.urlopen(req)# <http.client.HTTPResponse object at 0x000000000AA539B0>
```

##### 1、请求头添加

通过`urllib`发送的请求会有一个默认的Headers: “User-Agent”:“Python-`urllib`/3.6”，指明请求是由urllib发送的。所以遇到一些验证User-Agent的网站时，需要我们自定义Headers把自己伪装起来。

```python
headers = {
        'user-agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.117 Safari/537.36'
    }
print(headers)
url = 'http://www.baidu.com'
req = request.Request(url=url, headers=headers)
```

##### 2、操作cookie

在开发爬虫过程中，对cookie的处理非常重要，`urllib`的cookie的处理如下案例：

```python
from urllib import request
from http import cookiejar

# http模块：收集了多个用于处理超文本传输协议的模块。
# http.client 是一个低层级的 HTTP 协议客户端；对于高层级的 URL 访问请使用 urllib.request
# http.server 包含基于 socketserver 的基本 HTTP 服务类
# http.cookies 包含一些有用来实现通过 cookies 进行状态管理的工具
# http.cookiejar 提供了 cookies 的持久化

# 创建一个cookie对象
cookie = cookiejar.CookieJar()  # 
# 创建一个cookie处理器
cookies = request.HTTPCookieProcessor(cookie)
# 以它为参数，创建Openner对象
openner = request.build_opener(cookies)
# 使用这个openner来发请求
res = openner.open('http://www.baidu.com')

print(cookies.cookiejar)
```



##### 3、设置代理

运行爬虫的时候，经常会出现被封IP的情况，这是我们九需要使用IP代理来处理，urllib的IP代理的设置如下：

```python
from urllib import request

url = 'http://httpbin.org/ip'
# 代理地址
proxy = {'http':'180.76.111.69:3128'}
# 代理处理器
proxies = request.ProxyHandler(proxy)
# 创建opnner对象
opnner = request.build_opener(proxies)
res = opnner.open(url)
print(res.read().decode())
```

Response对象

`urllib`库中的类或者方法，在发送网络请求后，都会返回一个`urllib.response`的对象。它包含了请求回来的数据结果。它包含了一些属性和方法，供我们处理返回的结果。

1、read()获取相应返回的数据，只能用一次 `print(response.read())`

2、`readline()`读取一行

```python
while True:
    data = respnse.readline()
    if data:
        print(data)
```

3、info()获取响应头信息`print(response.info())`

4、geturl()获取访问的url `print(response.geturl())`

5、getcode() 返回状态码 `print(response.getcode())`

#### `urllib.parse`

`parse`模块是一个工具模块，提供了需要对`url`处理的方法，用于解析`url`。

##### `parse.quote()`

`url`中只能包含ascii字符，在实际操作过程中 `url` 传递的参数中会有大量的特殊字符，例如汉字，那么就需要进行 `url`编码。

```python
url = 'http://httpbin.org/get?aaa={}'
safe_url = url.format(parse.quote('心蓝'))
print(safe_url)
```

利用`parse.unquote()`可以反编码过来

##### `parse.urlencode()`

在发送请求的时候，往往会需要传递很多参数，如果用字符串方法去拼接会比较麻烦， `parse.urlencode()`方法就是用来拼接 `url`参数的。

```python
params = {'wd':'测试','code':1,'height':'188'}
res = parse.urlencode(params)
print(res)
```

`parse.parse_qs()`转回字典

`print(parse.parse_qs('wd=%E6%B5%8B%E8%AF%95&code=1&height=188'))`

#### `urllib.error`

`error`模块主要负责处理异常，如果请求出现错误，我们可以用 `error`模块进行处理主要包含 `URLError`和 `HTTPError`

1、 `URLError`是error异常模块的基类，由 `request`模块产生的异常都可以用这个类来处理

2、`HTTPError`是 `URLError`的子类，主要包含三个属性：

1. Code:请求的状态码
2. reason：错误的原因
3. headers:响应的报头

```python
try:
    response = request.urlopen(req)
    html = response.read().decode('utf-8')
    print(html)
except error.HTTPError as e:
        print(e.code) # 404
except error.URLError as e:
    print(e.reason) # 
```



#### `urllib.robotparse`

负责处理爬虫协议文件，robots.txt 的解析

https://www.taobao.com/robots.txt

**Robots协议（也称为爬虫协议、机器人协议等）的全称是“网络爬虫排除标准”（Robots Exclusion Protocol），网站通过Robots协议告诉搜索引擎哪些页面可以抓取，哪些页面不能抓取。**



**robots.txt文件是一个文本文件，使用任何一个常见的文本编辑器，比如Windows系统自带的Notepad，就可以创建和编辑它 [1] 。robots.txt是一个协议，而不是一个命令。robots.txt是搜索引擎中访问网站的时候要查看的第一个文件。robots.txt文件告诉蜘蛛程序在服务器上什么文件是可以被查看的。**

```
搜索引擎的自动提取信息会遵循一定的算法，但是，无论算法如何，第一步都是在寻找这个文件。其含义是，“贵站对我们这些Robots有什么限制？”所谓的Robots就是搜索引擎派出的蜘蛛或者机器人。如果没有得到回应（没有找到这个文件），代表没有什么限制，尽管来抓取吧。如果真的有这个文件，机器人会读来看看，如果自己被拒绝就会停止抓取过程了。
/ 禁止
```



### 1.2 发送请求与获得响应

#### 1.2.1 `urllib`函数发送HTTP GET请求

`urllib`最基本的一个应用：向服务端发送请求并获得响应

**`URLopen`** 

```python
import urllib.request
response = urllib.request.urlopen('https://baidu.com')
# 将服务端的响应数据用utf-8解码
print(response.read().decode('utf-8'))
```

`response`类型 `<class 'http.client.HTTPResponse'>`

包含read、getheader、getheaders等方法

```python
import urllib.request
response = urllib.request.urlopen('https://baidu.com')
# 将服务端的响应数据用utf-8解码
# 正文
print(response.read().decode('utf-8'))
print('status:',response.status)# 响应状态
print('msg:',response.msg)# 响应消息
print('version:',response.version)# HTTP版本
print('headers:',response.getheaders())# 所有响应头信息
print('header:',response.getheader('Content-Type'))# 指定的响应头信息
```

#### 1.2.2 `urlopen` 函数发送HTTP POST请求 

`urlopen`函数默认情况下发送的是HTTP GET请求，如果发送POST请求

```python
import urllib.request
import urllib.parse
#测试网址：http://httpbin.org/post

#定义一个字典参数
data_dict={"username":"zhangsan","password":"123456"}
#使用urlencode将字典参数序列化成字符串
data_string=urllib.parse.urlencode(data_dict)
#将序列化后的字符串转换成二进制数据，因为post请求携带的是二进制参数
last_data=bytes(data_string,encoding='utf-8')
#如果给urlopen这个函数传递了data这个参数，那么它的请求方式则不是get请求，而是post请求
response=urllib.request.urlopen("http://httpbin.org/post",data=last_data)
#我们的参数出现在form表单中，这表明是模拟了表单的提交方式，以post方式传输数据
print(response.read().decode('utf-8'))
```

## urllib3

urllib3 是一个基于python3的功能强大，友好的http客户端。越来越多的python应用开始采用urllib3.它提供了很多python标准库里没有的重要功能。

```python
import urllib
http = urllib.PoolManager()
r = http.request('GET','http://www.baidu.com')
print(r.status)
print(r.data)
```

<img src="C:\Users\dar\AppData\Roaming\Typora\typora-user-images\image-20200131192841015.png" alt="image-20200131192841015" style="zoom:200%;" />











