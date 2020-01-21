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

##### 请求头添加

通过`urllib`发送的请求会有一个默认的Headers: “User-Agent”:“Python-`urllib`/3.6”，指明请求是由urllib发送的。所以遇到一些验证User-Agent的网站时，需要我们自定义Headers把自己伪装起来。

```python
headers = {
        'user-agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.117 Safari/537.36'
    }
print(headers)
url = 'http://www.baidu.com'
req = request.Request(url=url, headers=headers)
```

##### 操作cookie

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



##### 设置代理

#### `urllib.error`

#### `urllib.parse`

#### `urllib.robotparse`

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





















