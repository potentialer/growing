# Scrapy 

怎么处理request和response对象

## 下载中间件API

实现特殊方法合属性的类

下载中间件是一个用来hooks进Scrapy的request/response处理过程的框架。

它是一个轻量级的底层系统，用来全局修改scrapy的request和response。

**scrapy**框架中的下载中间件，是实现了特殊方法的类。**scrapy**系统自带的中间件被放在**DOWNLOADER_MIDDLEWARES_BASE**设置中。用户自定义的中间件需要在**DOWNLOADER_MIDDLEWARES**中进行设置。设置是一个dict，键是中间件类路径，期值是中间件的顺序，是一个正整数0-1000越小越靠近引擎。

每个中间件都是Python的一个类，它定义了以下一个或多个方法：

- process_request（request，spider）  处理请求，对于通过中间件的每个请求调用此方法
  - 返回值：None(正常)、Response(直接调用process_response)、Request(重新开始)、异常（process_exception）
- process_response(request, response, spider)    处理响应，对于通过中间件的每个响应，调用此方法
  - response(正常)、request(重定向)
- process_exception(request, exception, spider)    处理请求时发生了异常调用
  - 处理异常
- from_crawler（cls，crawler ）
  - 引擎实例化其他的组件，赋值给中间件的对象

### 内置下载中间件

```
常用内置中间件：settings
CookieMiddleware   		# 支持cookie，通过设置COOKIES_ENABLED 来开启和关闭
HttpProxyMiddleware	# HTTP代理，通过设置request.meta['proxy']的值来设置
UserAgentMiddleware	# 与用户代理中间件。
其他中间件见官方文档：
https://docs.scrapy.org/en/latest/topics/downloader-middleware.html
```

## 自定义User-Agent

自定义UA池

```python 
class RandomUserAgentMiddleware（object）：
	def_init_（self，user_agents）：
    	self.user_agents =user_agents
    #从settings中获取设置文件
    @classmethod 
    def from_crawler（c1s，crawler）：		
        s=c1s（user_agents=crawler.settings.get（'MY_USER_AGENT'））
        crawler.signals.connect（s.spider_opened，signal=signals.spider_opened）
        return s
    
    #随机获取一个设置
    def process_request（self，request，spider）：
    	agent=random.choice（self.user_agents）
        request.headers['User-Agent'] = agent 
        return None # 正常，给下一个process_request
```

settings

```
MY_USER_AGENT=[多个UA]
需要注册
DOWNLOADER_MIDDLERWRES={
	'douban.middlers.RandomUserAgent':500,# 900靠近下载器
	# 注销
}
```

验证：

```
shell :
scrapy shell http:httpbin.org
response.headers['user-agent']
fetch('http:httpbin.org')
response.headers['user-agent']
```

## Scrapy.setting

### 常用设置

```python
命令行选项(优先级最高)# scrapy crawl spider -s LOG_FILE=scrapy.log
设置per-spider# scrapy源码
项目设置模块# settings
各命令默认设置# 属性值访问
默认全局设置(低优先级)# 默认的全局设置scrapy.settings.defaultsettings
```

BOT_NAME 项目名称

CONCURRENT_ITEMS item处理最大并发数，默认100

CONCURRENT_REQUESTS 下载最大并发数

CONCURRENT_REQUESTS_PER_DOMAIN 单个域名最大并发数

CONCURRENT_REQUESTS_PER_IP 单个ip最大并发数

## Scrapy对接Selenium

```
pip install selenium
chrome的webdriver： http://chromedriver.storage.googleapis.com/index.html 

Firefox Firefox驱动下载地址为：https://github.com/mozilla/geckodriver/releases/ 

IE浏览器驱动下载地址为：http://selenium-release.storage.googleapis.com/index.html （不推荐，没人用）

根据操作系统，以及浏览器版本，下载相应的驱动
并且将下载的webdriver的路径设置到环境变量中
# 训练网址：https://www.aqistudy.cn/historydata/
```

