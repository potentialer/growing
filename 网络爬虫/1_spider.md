# 认识爬虫

## 爬虫概念

什么是爬虫？

爬虫的概念：网络爬虫也叫网络蜘蛛，它特指一类自动批量下载网络资源的程序，这是一个比较口语化的定义。
更加专业和全面对的定义是：网络爬由是伪装成客户端与服务端进行数据交互的程序。

爬虫有什么用？

1. 数据采集
2. 搜索引擎
3. 模拟操作

### 重点难点

1. 数据的获取。图灵测试，反爬。
2. 采集速度。并发、分布式。

#### 案例

```python
import socket
# 创建客户端
client = socket.socket()

# socket.socket(socket_family, socket_type, protocol=0)
# socket_family 可以是 AF_UNIX(基于文件类型的套接字家族) 或 AF_INET(基于网络类型的套接字家族)。
# socket_type 可以是 SOCK_STREAM 或 SOCK_DGRAM。
# protocol 一般不填,默认值为 0。

# 连接百度服务器
client.connect(('www.baidu.com', 80))
# client.connect_ex()
# 构造http请求报文
data = b'GET / HTTP/1.0\r\nHost:www.baidu.com\r\n\r\n'
# 发送报文
client.send(data)
res = b''
# 接收相应数据
temp = client.recv(4096)

while temp:
    print('**********')
    res += temp
    temp = client.recv(4096)
    print(res)
```



## 会话技术

### Cookie

HTTP无状态，服务端不能区分两次请求是同一个用户的。不断的重新提交表单信息。

服务端要给用户请求打标签（门票、令牌）。响应中添加`setCookie`（键值对（session：个人的ID号））保存在客户端的本地，浏览器完成。——服务端设置cookie响应，客户端保存cookie到本地（浏览器完成）。

去银行不带银行卡，就算柜员是你老婆，她也不敢给你取钱。

很多的网站密码存放在Cookie中实现免登录。**不安全**的方式，在使用金钱的网站的时候一定退出。

### session

会话

一次会话：网站开始到窗口关闭。

意义：在会话期间内的所有操作有效的保存在服务器。

实现客户端的状态识别。因此，**session是基于Cookie的**。

![session](https://images2015.cnblogs.com/blog/1104082/201702/1104082-20170210212817682-1075777716.png)

在用户1和用户2登录的时候，我们的服务器在他们登录成功后，在session表中为他们每个用户分配了一个`sessionid`并且存下了一个对应的信息。当用户第二次访问该服务器的时候，会将`sessionid`在request请求中携带者发送过去。这时我们的服务器就可以根据`sessionid`确定用户存储的数据，然后进行使用。如图所示：

![session](https://images2015.cnblogs.com/blog/1104082/201702/1104082-20170210214818072-725308163.png)

####  session的生命周期

​     当session超过一定时间（一般为30分钟）没有被访问时，服务器就会认为这个session对应的客户端已经停止活动，然后将这个session删除。用以节省空间。当用户关闭浏览器时，`sessionId`的信息会丢失，虽然服务器session还在，依然无法访问到session中的数据。



### Cookie 和 Session 的区别

Cookie：保存在客户端

Session：保存在服务器。由客户端传上来的SessionID来进行判定，安全性高。

## socket

**Chrome进行HTTP抓包：图片抓取。**

```python
# https://baijiahao.baidu.com/s?id=1612392982674092834&wfr=spider&for=pc
General: # 一般信息：
	URL: # 请求的URL
	Method: # 请求方法
	status Code:304 # 缓存
	Remote Address:# 远程地址(HTTP:80,HTTPS:443)
	Remote Policy: # 远程策略
Response Headers: # 响应头
	accept:# 接收信息类型
	accept-Encoding: # 可接受编码格式
    accept-language: # 可接受语言类型
    cache-control：# 缓存控制
    cookie：# 键值对
    Host: # 域名
    user-agent：# 用户代理
Request Headers:# 请求头
    accept: # 请求信息类型
	accept-encoding: # 允许的编码格式
	accept-language: # 允许的语言
	cache-control: # 缓存控制
    user-agent: # 用户代理
```

### **案例：使用socket下载一张图片**

Eg: https://www.2717.com/

**在Chrome抓包中第一个获取到的请求的一定是主页面页面**，在network中获取目标数据的请求站点。

```python
# 抓取流程
1、Chrome抓包中network查看所有请求。查找图片请求所在的位置，所有的网站图片一定是在某台服务器下载回来的。
2、图片类型的请求可使用 IMG 选项快捷查找，检查请求中的 Preview 渲染内容，确认目标图片。
3、确认请求URL。
4、编写代码，请求数据。
```

### 示例代码

```python
"""
利用socket下载一张图片
"""
import socket
# 创建一个socket
# client=socket.socket()# 实例化HTTP请求
client = ssl.wrap_socket(socket.socket())  # 实例化HTTPS请求
"https://t1.hddhhn.com/uploads/tu/201905/406/14g54fd.jpg"
# 构造HTTP请求报文
# 报文格式：请求方式  资源路径  协议/协议版本\r\n请求头信息\r\n\r\n
data = "GET /uploads/tu/201905/406/14g54fd.jpg HTTP/1.1\r\nHost: t1.hddhhn.com\r\n\r\n"
# 连接服务端
# client.connect(('pic.sc.chinaz.com', 80))# Http
client.connect(('t1.hddhhn.com', 443))  # Https
# 发送请求(二进制传输)
client.send(data.encode()) 
# 接收响应
# 第一次数据：拿取图片总大小数据
img_first_data = client.recv(1024) 
# print(img_first_data) # \r\n\r\n之后的都是我们的内容
	# 我们先拿长度响应数据内容的长度：必须提供的字段。否则不知道什么时候响应结束
length = int(re.findall(b'Content-Length: (.*?)\r\n', img_first_data)[0])
# print(length)
# 获取图片数据(二进制接收)
img_data = b''
# 获取第一次请求里的图片数据，根据\r\n\r\n
img_data += re.findall(b'\r\n\r\n(.*)', img_first_data, re.S)[0]
# 获取剩余的数据
while True:
    temp = client.recv(1024)
    if temp:
        img_data += temp
    else:
        break
    if len(img_data) >= length:  # 判断接收数据是否与传输数据大小相同
        break
print(len(img_data), length)
# 写文件
with open('test.jpg', 'wb') as f:
    f.write(img_data)

```

















