# Scapy

## 介绍

Scrapy是纯Python开发的一个高效,结构化的网页抓取框架；

Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。 其最初是为了页面抓取 (更确切来说, 网络抓取 )所设计的，也可以应用在获取API所返回的数据(例如 Amazon Associates Web Services ) 或者通用的网络爬虫。 Scrapy用途广泛,可以用于数据挖掘、监测和自动化测试 Scrapy使用了Twisted 异步网络库来处理网络通讯。

## 模块安装

**依赖包：**

Windows:

- Twisted
  - https://www.lfd.uci.edu/~gohlke/pythonlibs/#Twisted
  - pip install Twisted文件名

Linux:

- 依赖
  - sudo apt-get install python-dev python-pip libxml2-dev libxslt1-dev zlib1g-dev libffi-dev libssl-dev
- Python3
  - sudo apt-get install python3-dev

**安装：**`pip install scrapy`

## 框架

### 新建项目：

`scrapy startproject <project name>[project_dir]`

### 框架架构：

```
__init__.py
scrapy.cfg # 项目的配置文件
<spider name>/ # 该项目的python模块。之后您将在此加入代码。
	__init__.py
	items.py # 项目中的item文件.
	middlewares.py # 中间件文件
	pipelines.py # 项目中的pipelines文件.
	settings.py # 项目的设置文件.
	spiders/ # 放置spider代码的目录.
		__init__.py
```

#### Item

item 是保存爬取的数据的容器；使用方法和python字典类似，并且提供了额外的保护机制来避免拼写错误导致的未定义字段错误。

类似在ORM中做的一样，可以通过创建一个 `scrapy.Item` 类， 并且定义类型为 `scrapy.Field` 的类属性来定义一个Item。

首先根据需要从[目标站点](http://book.zongheng.com/)获取到的数据对item进行建模。 我们需要从网站中获取小说名，url，以及网站的描述。 对此，在item中定义相应的字段。编辑<spider name>目录中的 `items.py` 文件:

```python
# <spider name>/item.py

```



```python
# 重写start_requests方法
    def start_requests(self):
        # 浏览器用户代理
        headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.87 Safari/537.36'
        }
        # 指定cookies
        cookies = {
            'cookie':'value'}
        urls = [
            'https://www.zhipin.com/job_detail/?query=python&city=101010100&industry=&position='
        ]
        for url in urls:
            yield scrapy.Request(url=url, headers=headers, cookies=cookies, callback=self.parse)

```

## Scrapy Shell

用来调试Scrapy 项目代码的 命令行工具。启动的时候预定义了Scrapy的一些对象启动。

作用：**调试 调试 调试**

### 设置shell

Scrapy 的shell是基于运行环境中的python 解释器shell本质上就是通过命令调用shell，并在启动的时候预定义需要使用的对象scrapy允许通过在项目配置文件”scrapy.cfg”中进行配置来指定解释器shell

例如：

```python
[settings]
shell = ipython
```

### 启动shell

启动语法`scrapy shell [option][url/file]`  注意(分析本地文件一定带上路径，scarpy shell默认是解析url)

Scrapy shell 本质上就是个普通的python shell

只不过提供了一些需要使用的对象，快捷方法便于我们调试。



快捷方法：

```python
•shelp()  # 打印可用对象及快捷命令的帮助列表
•fetch(url[,redirect=True]) # 根据给定的请求(request)或URL获取一个新的response，并更新相关的对象  重定向
•fetch(request)# req = scrapy.Requests(url,headers,cookies)
•view(response)# 在本机的浏览器打开给定的response。 其会在response的body中添加一个 <base> tag ，使得外部链接(例如图片及css)能正确显示。 注意，该操作会在本地创建一个临时文件，且该文件不会被自动删除。
```

scrapy 对象：

```python 
•crawler #  当前 Crawler 对象.引擎
•spider # 处理URL的spider。 对当前URL没有处理的Spider时则为一个 Spider 对象。
•request # 最近获取到的页面的 Request 对象。 您可以使用 replace() 修改该request。或者 使用 fetch 快捷方式来获取新的request。
•response # 包含最近获取到的页面的 Response 对象。
•sel # 根据最近获取到的response构建的 Selector 对象。
•settings# 当前的 Scrapy settings  eg:settings['piplines']
```

## Scrapy 选择器

Scrapy提供基于lxml库的解析机制，它们被称为选择器。因为，它们“选择”由XPath或CSS表达式指定的HTML文档的某部分。Scarpy选择器的API非常小，且非常简单。

`from scrapy.selector import Selector`

`from scrapy.http import HtmlResponse`

```python
# 从文本构造
body = '<html><body><span>good</span></body></html>'
Selector(text=body).xpath('//span/text()').extract()
Selector(text=body).xpath('//span/text()').extract_first()
# 从响应构造
response = HtmlResponse(url='http://example.com',body=body.encode())
Selector(response=response).xpath('//span/text()').extract_first()
response.selector.xpath('//span/text()').extract()
response.xpath('//span/text()').extract()
# scrapy 文档服务器
doc_url = "http://doc.scrapy.org/en/latest/_static/selectors-sample1.html"
scrapy shell http://doc.scrapy.org/en/latest/_static/selectors-sample1.html
response.xpath('//title/text()').extract()
# css选择器
response.css('title::text').extract()
```

**嵌套选择器**

```python
response.xpath('//a[contains(@href,"image")]')
links = response.xpath('//a[contains(@href,"image")]').extract()
for index,link in enumerate(links):
  print(index,link.xpath('@href').extract_first(),linkxpath('img/@src').extract_first())
response.css('img').xpath('@src').extract()
# 正则表达式
response.xpath('//a[contains(@href,"imgage")]/text()').re(r'Name:\s*(.*)') # 不可以再接xpath因为是字符串了
response.xpath('//a[contains(@href,"imgage")]/text()').re_first(r'Name:\s*(.*)')
```

## Scrapy.Spider

name spider 的名称

start_urls  起始urls

customer_settings 自定义设置运行此爬虫时将覆盖项目范围的设置，必须将其定义为类属性，因为在实例化之前更新了设置

logger  使用Spider创建Python日志器。

from_crawler   创建spider的类方法

statr_requests()   开始请求 必须返回一个iterable包含的是第一个请求，他只会被调用一次。

parse(response)  默认回调函数

close()  关闭  spider调用

## CrawlSpider

创建：`scrapy genspider -t crawl  hr.tencent hr.tencent.com`

hr_tencent.py

```python
rules # 包含一个或多个规则对象。爬取的特定的参数。解析连接(LinkExtractor)
parse_item # 解析
```

Rule类

```python

callback # 字符串或者self.函数名
cb_kwargs # 传递给回调函数的参数
follow  # 是否继续在现在的页面解析
proess_links # 过滤链接
proess_request # 过滤requests
```

LinkExtractor  从response提取url（链接提取器）

```python
allow # 正则表达式必须要有
deny # 不要的链接，优先级更高
allow_domain # 单个字符串也可以列表
restrict_xpath # 只解析xpath的内容
tags # 需要的标签
attrs # 需要的属性
can # url的规范化
uniue # 唯一的
process_value # 过滤的每一个进行处理
deny_extensions # 忽略的扩展名
```

## 实战项目

```python
rules = (
	Rule(LinkExtractor(allow=r'http://book.zongheng.com/store/c0/c0/b0/u0/p\d+/v9/s1/t0/u0/i1/ALL.html'),callback='parse_item',follow=True),
    Rule(LinkExtractor(allow=r'http://book.zongheng.com/book/\d+.html',callback='parse_detail_item',follow=False))
)
```

items  修改 列表页item()  DetailItem 详情页

pipelines

```python
# 所有的item都会经过
# 判断item
from .items import 详情item,DatailItem 
class XXXPipeline(object):
    def process_item(self,item,spider):
        if isinstance(item,详情item)：
        	content = json.dumps(dict(item),ensure_ascii=False)
            self.f1.write(content)
            self.f1.write('\n')
        elif isinstance(item,DetailItem):
            content = json.dumps(dict(item),ensure_ascii=False)
            self.f2.write(content)
            self.f2.write('\n')
def open_spider(self,spider):
    self.f1  =open('novel_list.json','w',encoding='utf-8')
    self.f2  =open('novel_detail.json','w',encoding='utf-8')
def close()
```

运行：`scrapy crawl hr.tencent`

## CrawlSpider 去重

parse_start_url   可以复写

parse  私有方法

scrapy去重 `from scrapy.dupefilters import RFPDupeFilter`

​	requests_fingerprint(self.request) 去重

​	自定义filter  需要配置：settings 

```
DUPEFILTER_CLASS="tanzhou.coustor_filter.URLFilter"
```

## Request

**Scrapy.http.Request**

```python
Scrapy.http.Request类是scrapy框架中request的基类。它的参数如下：
url #（字符串） - 此请求的URL
callback # （callable）- 回调函数
method # （string） - 此请求的HTTP方法。默认为'GET'。
meta # （dict） - Request.meta属性的初始值。
body #（str 或unicode） - 请求体。如果没有传参，默认为空字符串。
headers #（dict） - 此请求的请求头。
cookies #  - 请求cookie。
encoding # （字符串） - 此请求的编码（默认为'utf-8'）此编码将用于对URL进行百分比编码并将body转换为str（如果给定unicode）。
priority #（int） - 此请求的优先级（默认为0）。
dont_filter #（boolean） - 表示调度程序不应过滤此请求。
errback #（callable） - 在处理请求时引发任何异常时将调用的函数。
flags # （list） - 发送给请求的标志，可用于日志记录或类似目的。
```

**属性和方法**

```python
url #包含此请求的URL的字符串。该属性是只读的。更改请求使用的URL replace()。
method  #表示请求中的HTTP方法的字符串。
headers #类似字典的对象，包含请求头。
body # 包含请求正文的str。该属性是只读的。更改请求使用的URL replace()。
meta # 包含此请求的任意元数据的字典。传递参数
copy()#  返回一个新的请求，改请求是此请求的副本。
replace（[ URL，method，headers，body，cookies，meta，encoding，dont_filter，callback，errback] ）#  返回一个更新对的request
```

**FormRequest**

```python
get请求和post请求是最常见的请求。
scrapy框架内置了一个FormRequest类
它扩展了基类Request，具有处理HTML表单的功能。
它使用lxml.html表单，来预先填充表单字段，其中包含来自Response对象的表单数据。
具体API，学习源码
```

创建项目`scrapy startproject douban`

创建spider `scrapy genspider db accounts.douban.com`

**from_response()**

```python
参数：
response（Responseobject） - 包含HTML表单的响应
formname（string） - 如果给定，将使用name属性设置为此值的表单。
formid（string） - 如果给定，将使用id属性设置为此值的表单。
formxpath（string） - 如果给定，将使用与xpath匹配的第一个表单。
formcss（string） - 如果给定，将使用与css选择器匹配的第一个表单。
formnumber（整数） - 当响应包含多个表单时要使用的表单数。
formdata（dict） - 要在表单数据中覆盖的字段。。
clickdata（dict） - 用于查找单击控件的属性。
dont_click（boolean） - 如果为True，将提交表单数据而不单击任何元素。
```

```python
# 豆瓣登录

login_url = "https://accounts.douban.com/j/mobile/login/basic"
def parse(self,response):
	formdata={
		"ck":"",
		"name": "3003781845@qq.com",
		"password": "pythonvip",
		"remember": "false",
		"ticket": ""
	}
    headers={}# setting中UA
    ＂＂＂
    	yeild scrapy.FormRequest.from_response(response,formdata=formdata,callback=self.login)
    ＂＂＂
    yield scrapy.FormRequest(self.login_url,fromdata=fromdata,callback=self.login,headers=headers)
def login(self,response):
    res = response.xpath()
    if res is None:
        print("失败")
    else:
        print("成功")
```

**Response**

```python
参数：
url（字符串）# - 此响应的URL
status（整数）# - 响应的HTTP状态。默认为200。
headers（dict）# - 此响应的响应头。dict值可以是字符串（对于单值标头）或列表（对于多值标头）。
body（字节）# - 响应主体。要将解码后的文本作为str（Python 2中的unicode）访问，您可以使用response.text来自编码感知的 Response子类，例如TextResponse。
flags（列表）# - 是包含Response.flags属性初始值的列表 。如果给定，列表将被浅层复制。
request（Requestobject）# - Response.request属性的初始值。这表示Request生成此响应的内容。
```

**属性和方法：**

```
url
status
headers
body
request
meta
flags
copy（）
replace（[ url，status，headers，body，request，flags，cls ] ）
	# 修改
	response.replace(url="www.baidu.com")
		
urljoin（url ）# 像请求后添加res.urljoin(url="aaa")
follow（url） # 自动拼接域名
```

**日志配置和使用**

当然可以通过python的logging来记录。比如：logging.warning('This is a warning!')但是为了后期维护方面，我们可以创建不同的记录器来封装消息。并且使用组件或函数的名称进行命名，见左图案例:

**配置**

```python
这些设置可用于配置日志记录：
LOG_FILE		日志输出文件，如果为None，就打印在控制台
LOG_ENABLED	是否启用日志，默认True
LOG_ENCODING   	日期编码，默认utf-8
LOG_LEVEL	日志等级，默认debug
LOG_FORMAT	日志格式
LOG_DATEFORMAT	日志日期格式
LOG_STDOUT	日志标准输出，默认False，如果True所有标准输出都将写入日志中
LOG_SHORT_NAMES   短日志名，默认为False，如果True将不输出组件名

项目中一般设置：
LOG_FILE = 'logfile_name'
LOG_LEVEL = 'INFO'
```



## 暂停怎么使用

要启用一个爬虫的持久化，运行以下命令:

```
scrapy crawl somespider -s JOBDIR=crawls/somespider-1
```

然后，你就能在任何时候安全地停止爬虫(按Ctrl-C或者发送一个信号)。恢复这个爬虫也是同样的命令:

```
scrapy crawl somespider -s JOBDIR=crawls/somespider-1
```











