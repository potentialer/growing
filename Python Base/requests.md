# Requests

Cookie和Session的区别

Session（存放于服务端）基于Cookie（存放客户端、过期时间）

公共场所登录及时退出登录。

## 简介

Requests是一个优雅而简单的Python HTTP库，专为人类而构建。

Requests是有史以来下载次数最多的Python软件包之一，每天下载量超过400,000次。

之前的urllib做为Python的标准库，因为历史原因，使用的方式可以说是非常的麻烦而复杂的，而且官方文档也十分的简陋，常常需要去查看源码。与之相反的是，Requests的使用方式非常的简单、直观、人性化，让程序员的精力完全从库的使用中解放出来。

Requests的官方文档同样也非常的完善详尽，而且少见的有中文官方文档：http://cn.python-requests.org/zh_CN/latest/。

英文文档：http://docs.python-requests.org/en/master/api/



基于 `urllib`。优点：简单优雅。

从库中解放出来，不再多做请求的困扰。

例子：

```python
>>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
>>> r.status_code
200
>>> r.headers['content-type']
'application/json; charset=utf8'
>>> r.encoding
'utf-8'
>>> r.text
u'{"type":"User"...'
>>> r.json()
{u'private_gists': 419, u'total_private_repos': 77, ...}
```

## 功能特性

Requests 完全满足今日 web 的需求。

- Keep-Alive & 连接池
- 国际化域名和 URL
- 带持久 Cookie 的会话
- 浏览器式的 SSL 认证
- 自动内容解码
- 基本/摘要式的身份认证
- 优雅的 key/value Cookie
- 自动解压
- Unicode 响应体
- HTTP(S) 代理支持
- 文件分块上传
- 流下载
- 连接超时
- 分块请求
- 支持 `.netrc`

`source ~/.spiderenv/bin/activate` 

## 发起请求

Requests的请求不再像`urllib`一样需要去构造各种Request、opener和handler，使用Requests构造的方法，并在其中传入需要的参数即可。

每一个请求方法都有一个对应的API，比如GET请求就可以使用get()方法：

```python
import requests
resp = requests.get('https://www.baidu.com')
```

而POST请求就可以使用post()方法，并且将需要提交的数据传递给data参数即可：

```python 
import requests
resp = requests.post('http:httpbin.org/post',data={'key':'value'})
```

其他的请求方式也有对应的方法

```python
resp = requests.put('http:httpbin.org/put',data={'key':'value'})
resp = requests.delete('http:httpbin.org/delete')
resp = requests.head('http:httpbin.org/get')
resp = requests.options('http:httpbin.org/get')
```

自定义headers

```python
url = 'https://api.github.com/some/endpoint'
headers = {'user-agent':'my-app/0.0.1'}
resp = requests.get(url,headers=headers)
```

自定义Cookies

```python 
url = 'http://httpbin.org/cookies'
cookies = {'cookies_are':'working'}
resp = requests.get(url,cookies=cookies)
print(resp.text)
```

设置代理

```python 
proxies= {
    'http':'http://10.10.1.10:3128',
    'https':'http://10.10.1.10:1080',
}
requests.get('http://example.org',proxies=proxies)
```

重定向

```python 
resp = requests.get('http://github.com',allow_redirects=False)
print(resp.status_code)
```

禁止证书验证

有时候我们使用了抓包工具，这个时候由于抓包工具提供的证书并不是由受信任的数字证书颁发机构颁发的，所以证书的验证会失败，所以我们就需要关闭证书验证。

在请求的时候把verify参数设置为False就可以关闭证书验证了。

```python
resp = requests.get('http://httpbin.org/post',verify=False)
# 消除警告
from requests.packages.urllib3.exceptions import InsecureRequestWarning
# 禁用安全请求警告
requests.packages.urllib3.disable_waring(InsecurRequestWarning)
```

设置超时

```python
requests.get('http://github.com',timeout=0.001)
```

## 接收响应

### 响应内容

通过Requests发起请求获取到的，是一个`requests.models.Response`对象。通过这个对象我们可以很方便的获取响应的内容。

之前通过`urllib`获取的响应，读取的内容都是bytes的二进制格式，需要我们自己去将结果decode()一次转换成字符串数据。

而Requests通过text属性，就可以获得字符串格式的响应内容。

```python
import requests
resp = requests.get('https://api.github.com/events')
resp.text
```

### 字符编码

Requests会自动的根据响应的报头来猜测网页的编码是什么，然后根据猜测的编码来解码网页内容，基本上大部分的网页都能够正确的被解码。而如果发现text解码不正确的时候，就需要我们自己手动的去指定解码的编码格式。

```python
resp = requests.get('https://api.github.com/events')
resp.encoding = 'utf-8'
print(resp.text)
```

二进制数据

```python
print(resp.content)
```

`json`数据

```python
resp = requests.get('https://api.github.com/events')
print(resp.json())
```

状态码

```python
print(resp.status_code)
```

响应报头

```python
print(resp.headers)
```

服务器返回的cookie

`print(resp.cookies)`

URL

```python
import requests
params = {'key':'value1','key2':'values2'}
resp = requests.get('http://httpbin.org/get',params=params)
print(resp.url)
```

### Session

在Requests中，实现了Session(会话)功能，当我们使用Session时，能够像浏览器一样，在没有关闭关闭浏览器时，能够保持住访问的状态。

这个功能常常被我们用于登陆之后的数据获取，使我们不用再一次又一次的传递cookies。

```python
import requests
session = requests.Session()
session.get('http:///httpbin.org/cookies/set/sessioncookie/123456')
resp=session.get('http://httpbin.org/cookies')
print(resp)
```

首先我们需要去生成一个Session对象，然后用这个Session对象来发起访问，发起访问的方法与正常的请求是一摸一样的。

同时，需要注意的是，如果是我们在get()方法中传入headers和cookies等数据，那么这些数据只在当前这一次请求中有效。如果你想要让一个headers在Session的整个生命周期内都有效的话，需要用以下的方式来进行设置：

```python
# 设置整个headers
session.headers = {'user-agent':'my-app'}
# 增加一条headers
session.headers.update({'new-cookie':'true'})
```

12306登录案例

```python
# 1、访问首页面  获取cookie
# 2、验证用户名和密码  携带上一次的cookie
# 3、权限验证  携带上一次的cookie
# 一个会话保存
import requests 
def get_point_index(indexs):
    map = {
        '1':'39,43'
    }
    indexs = indexs.split(',')
    temp=[]
    for index in indexs:
        temp.append(map[index])
    return ','.join(temp)
session = requests.Session()
# 添加
session.headers.update(headers)
# 之后的requests都改为session
# 1、访问首页面
login_page_url = 'https://kyfw.12306.cn/otn/resources/login.html'
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.87 Safari/537.36"
}
response = requests.get(login_page_url,headers=headers)
# print(response.cookies)
cookies = response.cookies
# 2、下载验证码
captcha_url = 'https://kyfw.12306.cn/passport/captcha/captcha-image64?login_site=E&module=login&rand=sjrand&1580719683021&callback=jQuery19105017952810127901_1580719656494&_=1580719656495'
captcha_response = requests.get(captcha_url,headers=headers,cookies=cookies)
cookies = captcha_response.cookies
# print(captcha_response.text)# 拿到图片
# base_64  获取图片信息
img_data = re.findall(b'',captcha_response.content)[0]
# print(img_data) #图片二进制
res = base64.b64decode(img_data)
with open('cap.jpg','wb')as f:
    f.write(res)
# 验证验证码
check_captcha_api = "https://kyfw.12306.cn/passport/captcha/captcha-check"
args = {
    'callback: jQuery191028803489093952916_1580729079056',
	'answer': get_point_index(input('输入坐标：')),
	'rand': 'sjrand',
	'login_site': 'E',
	'_': '1580729079058',
}
check_response= requests. get(check_captcha_api, params=args, headers=headers，cookies=cookies) # 带cookie
print(check_response. text)







```





























