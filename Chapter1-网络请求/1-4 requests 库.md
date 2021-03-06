### requests 是什么？

[人类唯一的一个非转基因的 Python HTTP 库，人类可以安全享用。](http://cn.python-requests.org/zh_CN/latest/) 

### 1. 安装与调用 requests 
```python
pip install requests # 在 CMD 中运行
import requests      # 在 python 编辑器中调用
```

### 2. 获取响应内容

#### 2.1 requests 库的七个常用用法

| 方法                | 说明                                                 |
| ------------------- | ---------------------------------------------------- |
| requests.requests() | 构造一个请求，支撑以下各方法的基础方法               |
| requests.get()      | 获取 HTML 网页的主要方法，对应与 HTTP 的 GET         |
| request.head()      | 获取 HTML 网页头的方法，对应与 HTTP 的 HEAD          |
| requests.post       | 向 HTML 网页提交 POST 请求的方法 对应于 HTTP 的 POST |
| request.put         | 向 HTML 网页提交 PUT 请求的方法 对应于 HTTP 的 PUT   |
| requests.patch      | 向 HTML 网页提交局部修改请求，对应于 HTTP 的 PATCH   |
| requests.delete     | 向 HTML 网页提交删除请求，对应于 HTTP 的DELETE       |

#### 2.2 发送 GET 请求

1. 最简单的发送`get`请求就是通过`requests.get`来调用：

   ```python
   import requests
   url = ''
   resp = requests.get(url)
   ```

#### 2.3发送 POST 请求

1. 最基本的POST请求可以使用`post`方法：

   ```python 
   import requests
   url = ''
   response = requests.post(url,data=data)
   ```


#### 2.4 Response 对象的属性

| 属性                | 解释                                                         |
| ------------------- | ------------------------------------------------------------ |
| r.status_code       | 返回网页请求状态码 200为成功                                 |
| r.text              | 返回网页文本内容（服务器响应的内容）**注意**：text 后面没有括号！ |
| r.content           | 猜测返回对象的二进制形式                                     |
| r.encoding          | 返回服务器内容的编码方式 如 utf-8                            |
| r.apparent_encoding | 从内容中分析出的响应内容编码方式（备选编码方式）             |
| resp.json()         | 内置的 json 文件解析器，返回 json 文本                       |

```python
import requests #导入Requests库

def get_html_text(url):
    try:
        r = requests.get(url,timeout=20) # 设置超时
        #使用get方法发送请求，返回包含网页数据的Response并存储到Response对象r中
        r.raise_for_status() 
        # 如果 HTTP 请求返回了不成功的状态码， Response.raise_for_status() 会抛出一个 HTTPError异常
        r.encoding = r.apparent_encoding
        return r.text
    except:
        # 异常处理
        return '产生异常'
if __name__ == '__main__':
    url = ''
    print(get_html_text(url))
```

### 3. 定制 requests

#### 3.1 通过设置 params 字典定制 url 参数

如果要将参数放在url中传递，可以利用 params 参数。

#### 3.2 定制请求头 headers

- 通过 **fake_useragent** 包设置 headers{“User-Agent”}

- 官方文档： https://pypi.org/project/fake-useragent/

  ```python
  import requests
  url = ''
  kw = {'wd':'中国'}
  headers = {"User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36"}
  
  # params 接收一个字典或者字符串的查询参数，字典类型自动转换为 `url` 编码，不需要urlencode()
  response = requests.get(url,headers=headers,params = kw)
  
  # 查看响应内容，response.text 返回的是 Unicode 格式的数据
  print(response.text)
  # 查看响应内容，response.content 返回的字节流数据
  print(response.content)
  # 查看完整url地址
  print(response.url)
  # 查看响应头部字符编码
  print(response.encoding)
  # 查看响应码
  print(response.status_code)
  ```



#### 3.3 requests.post 请求中定制 data

1. 请求拉勾网的数据的代码：

   ```python
   # -*- coding: utf-8 -*-
   """
   Created on Thu Oct 11 13:22:35 2018
   @author: weiro
   """
   
   import requests
   url = "https://www.lagou.com/jobs/positionAjax.json?city=%E6%9D%AD%E5%B7%9E&needAddtionalResult=false"
   
   headers = {'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36',   'Referer':'https://www.lagou.com/jobs/list_%E7%88%AC%E8%99%AB?labelWords=&fromSearch=true&suginput='}
   data = {'first': 'true','pn': 1,'kd': '爬虫'}
   resp=requests.post(url,headers=headers,data=data,timeout=20)
   # 如果是json数据，直接可以调用json方法
   print(resp.json())
   ```



#### 3.4 设置超时 timeout

有时爬虫会遇到服务器长时间不返回，这时爬虫程序就会一直等待，造成爬虫程序没有顺利地执行。因此，可以用Requests在timeout参数设定的秒数结束之后停止等待响应。意思就是，如果服务器在timeout秒内没有应答，就返回异常。

#### 3.5 使用代理

使用`requests`添加代理也非常简单，只要在请求的方法中（比如`get`或者`post`）传递`proxies`参数就可以了。示例代码如下：

```python
import requests

url = "http://httpbin.org/get"

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36',
}

proxy = {
    'http': '171.14.209.180:27829'
}

resp = requests.get(url,headers=headers,proxies=proxy)
with open('xx.html','w',encoding='utf-8') as fp:
    fp.write(resp.text)
```

#### 3.6 cookie:

如果在一个响应中包含了`cookie`，那么可以利用`cookies`属性拿到这个返回的`cookie`值：

```python
import requests

url = "http://www.renren.com/PLogin.do"
data = {"email":"970138074@qq.com",'password':"pythonspider"}
resp = requests.get('http://www.baidu.com/')
print(resp.cookies)
print(resp.cookies.get_dict())
```

### 3.7 session

之前使用`urllib`库，是可以使用`opener`发送多个请求，多个请求之间是可以共享`cookie`的。那么如果使用`requests`，也要达到共享`cookie`的目的，那么可以使用`requests`库给我们提供的`session`对象。注意，这里的`session`不是web开发中的那个session，这个地方只是一个会话的对象而已。还是以登录人人网为例，使用`requests`来实现。示例代码如下：

```python
import requests

url = "http://www.renren.com/PLogin.do"
data = {"email":"970138074@qq.com",'password':"pythonspider"}
headers = {
    'User-Agent': "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
}

# 登录
session = requests.session()
session.post(url,data=data,headers=headers)

# 访问大鹏个人中心
resp = session.get('http://www.renren.com/880151247/profile')

print(resp.text)
```

### 3.8 处理不信任的SSL证书：

对于那些已经被信任的SSL整数的网站，比如`https://www.baidu.com/`，那么使用`requests`直接就可以正常的返回响应。示例代码如下：

```python
resp = requests.get('http://www.12306.cn/mormhweb/',verify=False)
print(resp.content.decode('utf-8'))
```

### 4.总结

在网站设计中，纯粹HTML格式的网页通常被称为静态网页，早期的网站一般都是由静态网页制作的。在网络爬虫中，静态网页的数据比较容易获取，因为所有数据都呈现在网页的HTML代码中。

