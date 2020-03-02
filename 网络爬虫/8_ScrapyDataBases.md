>>>scrapy startproject novel
>>>scrapy genspider quanshuwang www.quanshuwang.com
def parse():
"""收集小说主页URL地址"""
novel_url = response.xpath("")
for url in novel_urls:
	yield Request
def parse_novel_info(response):
# 小说名
# 作者
# 简介
# 类别
# 简介
# 状态
# url
# 创建时间 datetime.datetime.now()
# 章节URL
chapter_info_url=response
return scrapy.Rquest(chapter_info_url,call=parse_chapter_urls)
def chapter_urls():
# 小说章节名称和URL

yield item


# settings
数据库配置
DATABASE_CONFIG = {
'type':"mysql",
'config':{
'host':'127.0.0.1',
'port':3306,
user:root
password:root
db:novel
charset:utf8
}
}