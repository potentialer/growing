# `Scrapy_Redis` 

多台机器分布式抓取。在`scrapy`调度器放到 `redis` 数据库。

```python
# 配置调度器
# import scrapy_redis.scheduler.Scheduler
SCHEDULER= ''
# 过滤器，重复过滤，去重
# from scrapy_redis.duperfilter import RFPDupeFilter
DUPEFILTER_CLASS='scrapy_redis.duperfilter.RFPDupeFilter'
# redis 配置
REDIS_HOST = 'localhost'
REDIS_PORT = 6379
```

#### `redis-cli`

```python
key *
# 查询
zrange --.requests 0 1  # 序列化之后的数据
```

# `scrapy` 部署

### 怎么部署

1、云。2、`scrapyd`

安装：`pip install scrapyd`

启动：`scrapyd`  web页面可以访问

部署：安装工具：虚拟环境：`pip install scrapy-client`

修改：

```
scrapy.cfg
url = 服务器地址
```

`scrapyd-deploy novel -p novel` # p 项目名参数

##### 启动爬虫

**curl 服务器地址**

job 正在运行的爬虫

### 定时运行





















