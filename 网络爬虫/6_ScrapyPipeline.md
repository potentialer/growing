媒体管道
scrapy genspider baidu www.baidu.com
shell 调试百度图片 
parse():
	html=response.text
	img_urls = re.findall(r'"thumURL":"(.*?)"',html)
	for index,url in enumerate(img_urls):
		yield scrapy.Request(url=url,callback=self.parse_con)
def parse_con()
	item=Item()
	itrm['index'] = re
	item['content']=response.body
item():
	content=scrapy.Field()
	index=scrapy.Field()
ImagePipline(object):
	with open('%s.jpg'%item['index'],'wb') as f:
		f.write(item['content'])
	return item

使用：pipline
'scrapy.pipelines.images.ImagePipline'
items:image_urls=scrapy.Field()
def parse:
	item = ITEM()
	html=response.text
	img_urls = re.findall(r'"thumURL":"(.*?)"',html)
	item['imgage_urls'] = img_urls
	yield item
setting:  IMAGES_STORE='' #  存储路径
下载中间件：process_request
写个middlewares处理
	request.headers['Referer'] = ''
Scrapy设计的管道
重复下载一张图片，下载图片的时间要求。是否更新，指定位置，通用格式，缩略图，图片的宽高过滤（小图片不要了）。
什么是媒体管道：文件管道
from scrapy.pipelines.images import ImagesPipeline
item返回会进入管道
使用默认管道
image_urls = scrapy.Field() # 检测字段并进行图片管道处理
file_urls = scrapy.Field() # 文件管道
url 优先处理，被下载之后自动处理。

图片命名：散列加密
IMAGES_STORE = '绝对路径'
ITEM_PIPELINES = {'scrapy.pipelines.images.ImagesPipeline': 1}  启用
FILES_STORE = '/path/to/valid/dir'		文件管道存放位置
IMAGES_STORE = '/path/to/valid/dir'		图片管道存放位置
FILES_URLS_FIELD = 'field_name_for_your_files_urls'    自定义文件url字段
FILES_RESULT_FIELD = 'field_name_for_your_processed_files'   自定义结果字段
IMAGES_URLS_FIELD = 'field_name_for_your_images_urls'  自定义图片url字段
IMAGES_RESULT_FIELD = 'field_name_for_your_processed_images'  结果字段
FILES_EXPIRES = 90    文件过期时间   默认90天  90天内不会再下载
IMAGES_EXPIRES = 90    图片过期时间   默认90天
IMAGES_THUMBS = {'small': (50, 50), 'big':(270, 270)}  缩略图尺寸
    大图:IMAGES_STORE文件夹下full    thums/big/small
IMAGES_MIN_HEIGHT = 110   过滤最小高度
IMAGES_MIN_WIDTH = 110   过滤最小宽度
MEDIA_ALLOW_REDIRECTS = True    是否重定向
#重写方法
实现定制图片管道
图片路径存储
图片案例
scrapy genspider -t crawl img_meinv www.2717.com
item()
	image_urls=scrapy.Field()
parse():
	item['image_urls']
setting:
	'scrapy.pipelines.images.ImagePipline'
	IMAGES_STORE
	UA池添加


scrapyd
安装：pip install scrapyd
启动Scrapyd 
scrapyd