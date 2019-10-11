---
title: python
top: false
cover: false
toc: true
mathjax: true
summary: 记录学习python的点点滴滴
date: 2019-10-02 19:49:47
tags: 学习记录
categories: python
---

***python***
&emsp;&emsp;之前学的每门语言都是浅浅浅浅浅浅浅尝辄止看看能不能深入一点
## 文件读取
```python
# 读
f = open("/tmp/foo.txt", "w")

f.write( "Python 是一个非常好的语言。\n是的，的确非常好!!\n" )

# 关闭打开的文件
f.close()

##另一种写法
with open('TTBT.txt', 'r') as f:
    print(f.read())

```

***

```python
#写
# 打开一个文件
f = open("/tmp/foo.txt", "r")

str = f.read()
print(str)

# 关闭打开的文件
f.close()

#另一种写法
with open('TTBT.txt', 'a+') as f:
    f.write("ss")

```

## 爬虫
### 设置代理ip
查[代理ip](https://cn-proxy.com/)的网址
查[当前ip](http://icanhazip.com)的网址
```python
#2019-10-06 22:04:57
import urllib.request
import random
url = 'http://icanhazip.com'

#参数是一个字典{'类型','代理ip:端口号'}
iplist = ['124.156.108.71:82','183.146.213.157:80','39.137.69.6:80']
proxy_support = urllib.request.ProxyHandler({'http':random.choice(iplist)})

#定制、创建一个opener
opener = urllib.request.build_opener(proxy_support)

#改useragent
opener.addheaders = [('User-Agent','Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.71 Safari/537.36')]

#安装opener
urllib.request.install_opener(opener)

#调用
response = urllib.request.urlopen(url)

html = response.read().decode('utf-8')#.encode('GBK','ignore').decode('GBk')
print('当前代理IP是'+html)


```

### 贴吧图片
正则表达式写法很重要
```python
#2019-10-07 20:54:07
#fun.py
import urllib.request
import re
def  open_url(url):
  req = urllib.request.Request(url)
  req.add_header('User-Agent','Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36')
  page = urllib.request.urlopen(req)
  html = page.read().decode('utf-8')
  return html

def get_img_tieba(html,path=''):
  p = r'<img class="BDE_Image".*?src="([^"]+\.jpg)"'
  imglist = re.findall(p,html)

  # for each in imglist:
  #   print(each)

  for each in imglist:
    filename = path+each.split("/")[-1]
    urllib.request.urlretrieve(each,filename,None)


```

```python
import fun
import re

html=fun.open_url('https://tieba.baidu.com/p/6234466251')

fun.get_img_tieba(html,'F:/lkyblogcode/python/')

```
### Scarpy框架
2019-10-09 22:43:57
[文档]('http://www.scrapyd.cn/')
#### 创建一个Scrapy项目
cd到一个目录执行"scrapy startproject 名称
#### 定义Item容器
修改items.py的内容

```python
#items.py
import scrapy


class DmozItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    title = scrapy.Field()
    link = scrapy.Field()
    desc = scrapy.Field()

```

#### 编写爬虫
编写爬虫类Spider，Spider是用户编写用于从网站上爬取数据的类。其包含了一个用于下载的初始URL，然后是如何跟进网页中的链接以及如何分析页面中的内容，还有提取生成item的方法

```python
#dmoz_spider此为简化写法详见教程
import scrapy

class DmozSpider(scrapy.Spider):#需要继承scrapy.Spider类

  #scrapy crawl 'name'
  name = "dmoz"

  #网站
  allowed_domains = ['chinadmoz.org']

  #从哪里开始
  start_urls = [
    'http://www.chinadmoz.org/subindustry/9/',
    'http://www.chinadmoz.org/subindustry/14/'
    ]

  #把爬下来的网页代码保存
  def parse(self,response):
      filename = response.url.split("/")[-2]
      with open(filename,'wb') as f:
        f.write(response.body)

```

***

提取网页中需要的数据，在Scrapy中是使用一种基于XPath和CSS的表达式机制：Scrapy Selectors
Selector是一个选择器，它有四个基本的方法：
&emsp;&emsp;xpath():传入xpath表达式，返回该表达式所对应的所有节点的selector list列表。
&emsp;&emsp;css():传入Css表达式，返回该表达式所对应的所有节点的selector list列表。
&emsp;&emsp;extract():序列化该节点为unicode字符串并返回list。
&emsp;&emsp;re():根据传入的正则表达式对数据进行提取，返回unicode字符串list列表。

①在shell里实验
scrapy shell "url"之后使用上述方法筛选需要的信息(要用双引号)
response.xpath('//标签名')  选出所有标签的内容
response.xpath('//标签名').extract() ......并字符串化

response.xpath('//ul/li/a/@href')
response.xpath('//ul[@class="..."]/li')根据类名筛

②实验好了修改爬虫代码

```python
#dmoz_spider
import scrapy

class DmozSpider(scrapy.Spider):

  #scrapy crawl 'name'
  name = "dmoz"

  #网站
  allowed_domains = ['chinadmoz.org']

  #从哪里开始
  start_urls = [
    'http://www.chinadmoz.org/subindustry/9/',
    'http://www.chinadmoz.org/subindustry/14/'
    ]

  #爬取网址信息
  def parse(self,response):
    # filename = response.url.split("/")[-2]
    # with open(filename,'wb') as f:
    #   f.write(response.body)
    sel = scrapy.selector.Selector(response)
    sites = sel.xpath('//ul[@class="boxbdnopd"]/li')
    for site in sites:
        title = site.xpath('div[@class="listbox"]/h4/@title').extract()
        link = site.xpath('div[@class="listbox"]/h4/a/@href').extract()
        desc = site.xpath('div[@class="listbox"]/h4/a/text()').extract()
        print(title,link,desc)

```

命令行执行scrapy crawl 'id'


#### 存储内容

```python
#dmoz_spider
import scrapy
from tutorial.items import DmozItem

class DmozSpider(scrapy.Spider):

  #scrapy crawl 'name'
  name = "dmoz"

  #网站
  allowed_domains = ['chinadmoz.org']

  #从哪里开始
  start_urls = [
    'http://www.chinadmoz.org/subindustry/9/',
    'http://www.chinadmoz.org/subindustry/14/'
    ]

  #存到容器里
  def parse(self,response):
    # filename = response.url.split("/")[-2]
    # with open(filename,'wb') as f:
    #   f.write(response.body)
    sel = scrapy.selector.Selector(response)
    sites = sel.xpath('//ul[@class="boxbdnopd"]/li')
    items=[]
    for site in sites:
      item = DmozItem()
      item['title'] = site.xpath('div[@class="listbox"]/h4/@title').extract()
      item['link'] = site.xpath('div[@class="listbox"]/h4/a/@href').extract()
      item['desc'] = site.xpath('div[@class="listbox"]/h4/a/text()').extract()
      items.append(item)

    return items

```

命令行执行scrapy crawl dmoz -o items.json -t json
-o 文件名  -t 文件格式
修改输出文件编码：在settings.py加入FEED_EXPORT_ENCODING = 'utf-8'即可

### scrapy css选择器使用
[教程](http://www.scrapyd.cn/doc/146.html)
