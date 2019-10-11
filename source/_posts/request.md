---
title: requests库
top: false
cover: false
toc: true
mathjax: true
date: 2019-10-11 19:01:58
summary: 关于requests库
tags:
- 爬虫
- requests库
categories:
- python
---

## requests.request
![](1.png)
这些方法都是在requests.request的基础上创建的
```python
requests.request(method, url, **kwargs)

#以下两句等价
r = requests.get('https://api.github.com/events')
requests.request(method='get',url="https://api.github.com/events")

```

***
## requests.get
```python
这个方法可以接收三个参数，其中第二个默认为None 第三个可选
def get(url, params=None, **kwargs)

#作用是模拟发起GET请求
Sends a GET request.

#模拟获取页面的url链接
:param url: URL for the new :class:Request object.

#额外参数 字典或字节流格式，可选
:param params: (optional) Dictionary or bytes to be sent in the query string for the :class:Request.

# 十二个控制访问参数，比如可以自定义header
:param **kwargs: Optional arguments that request takes.

# 返回一个Response对象
:return: :class:Response <Response> object
:type: requests.Response

```

>kwargs: 控制访问的参数，均为可选项
 params : 字典或字节序列，作为参数增加到url中
 data : 字典、字节序列或文件对象，作为Request的内容 json : JSON格式的数据，作为Request的内容
 headers : 字典，HTTP定制头
 cookies : 字典或CookieJar，Request中的cookie
 auth : 元组，支持HTTP认证功能
 files : 字典类型，传输文件
 timeout : 设定超时时间，秒为单位
 proxies : 字典类型，设定访问代理服务器，可以增加登录认证
 allow_redirects : True/False，默认为True，重定向开关
 stream : True/False，默认为True，获取内容立即下载开关
 verify : True/False，默认为True，认证SSL证书开关
 cert : 本地SSL证书路径
 url: 拟更新页面的url链接
 data: 字典、字节序列或文件，Request的内容
 json: JSON格式的数据，Request的内容

***

## 常用的两个控制访问参数：

### 1.为了伪装成浏览器访问常设置head
```python
import requests

#常用Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36
hd = {'User-agent':'123'}

r = requests.get('http://www.baidu.com', headers=hd)
print(r.request.headers)
'''
OUT:
{'User-agent': '123', 'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive
'}
'''

```

### 2.设置代理
```python
import requests
pxs = { 'http': '124.156.108.71:82'}
r = requests.get('http://icanhazip.com/', proxies=pxs)
print (r.text)
#OUT::124.156.108.71


```
## 常用Response对象方法
```python
import requests
r = requests.get("http://www.baidu.com")

# 设置正确的编码方式(防止response.text编码错误)
r.encoding = r.apparent_encoding

#HTTP请求的返回状态，比如，200表示成功，404表示失败
print (r.status_code)

#HTTP请求中的headers
print (r.headers)

#从header中猜测的响应的内容编码方式
print (r.encoding)

#从内容中分析的编码方式（慢）
print (r.apparent_encoding)

# response.text 返回的是一个 unicode 型的文本数据
# response.content 返回的是 bytes 型的二进制数据
print (r.content)
print (r.text)

```

## 获取网页的通用框架
```python
import requests

def getHtmlText(url):
    try:
        r = requests.get(url, timeout=30)
        # 如果状态码不是200 则应发HTTOError异常
        r.raise_for_status()
        # 设置正确的编码方式
        r.encoding = r.apparent_encoding
        return r.text
    except:
        return "Something Wrong!"


```

## 把爬取的网页源代码保存为txt文件
```python
import request
html = request.getHtmlText("http://www.baidu.com")
with open('1.txt','a+') as f:
  f.write(html)
with open('1.txt','r') as f:
  print(f.read())

```
