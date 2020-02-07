from bs4 import BeautifuulSoup

soup=BeautifulSoup(html)

标题：soup.title

类型：type(soup.title)

内容：soup.title.string

父标签：soup.title.string

## tag

```a
from bs4 import BeautifuulSoup

soup=BeautifulSoup(html,'lxml')
p = soup.p
type(p)
p.name # 标签名
a = soup.a
a['class']# 属性
多个值是列表形式返回
a.get_text()# 下边所有的内容
body = soup.body()
body.get_text()# 所有的文本内容
```

type(a.get_text())# 字符串

type(p.string)# 对象（不常用）strings

type(body.string)# none

Tag的遍历（节点的选择）

body下所有的节点：body.contents

包含所有的直接节点迭代器（只要儿子）：body.children

包含所有节点迭代器：body.descendantes

p.string # 标签中只有一个的字符串 

body.strings# 生成器包含所有的内容

body.stripped_strings # 去掉所有的空白行

p.parent # 他的爸爸

p.parents # 他的所有父辈

p.next_sibling # 下一个节点

p.previous_sibling# 上一个节点

p.next_siblings  # 下所有的节点

p.previous_siblings # 上所有的节点

soup.find_all(['a','b'])# 所有标签

soup.find_all(attrs={'class'='sister'})# 属性

soup.find_all(text='Ejsie')# 只找到字符串

soup.find_all # 找到所在的a标签

soup.find_all(text='Ejsie')[0].parent # ResultSet

soup.html.find_all("title", recursive=False)# 限制在子节点

tags = soup.find_all(re.compile("^b"))#正则

### CSS选择器

soup.select('title')

soup.select('p > a')

```html
<html><head><title>The Dormouse's story</title></head>
<body>
<pctass=# titte"><b>The Dormouse's story</b></p>
<p class=l"story">Once upon a time there were three tittle sisters; and their names were
<a href="http://example. com/elsien class="aisteru id="tink1">E1sie</a>,
<a href=# http://example. com/lacie"class="sister"id="1inkz">Lacie</a> and
<a href=# http://exampte. com/til1ie"ctass=Msister"id="tinks">Tiltie</a>; and they tived at the bottom of a wet1.</p>
<p class=llatory">...</p>
```





