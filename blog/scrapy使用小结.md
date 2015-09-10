title: "scrapy使用小结"
date: 2015-09-10 15:08:46
tags:
---

这篇文章来总结一下初次使用scrapy的经验。

### 简介
scrapy是基于python的一个强大的爬虫框架，用于抓取web站点并从页面中提取结构化的数据。

scrapy使用[Twisted](http://twistedmatrix.com/trac/)这个异步网络库来处理网络通讯，包含了各种中间件接口，可以灵活的完成各种需求。整体架构如下图所示：
![pic](/image/scrapy/40238622_1.png)

### 安装
可以使用pip安装scrapy，安装的时候scrapy需要的一些依赖包也会一并安装。这里使用豆瓣的py镜像，这样安装速度会比较快。

```shell
pip install scrapy -i http://pypi.douban.com/simple
```

### 第一个爬虫
接下来开始编写第一个爬虫，以抓取知乎用户为例。

#### 新建项目
首先，需要定义一个scrapy项目，运行命令：

```shell
scrapy startproject zhihuspider
```

会创建一个zhihuspider项目，包含如下内容：
![pic](/image/scrapy/1.jpg)

其中：

* scrapy.cfg: 项目的配置文件
* zhihuSpider/: 该项目的python模块，本爬虫的代码会存放在此处。
* zhihuSpider/items.py: 项目中的item文件，用来定义存储数据的item结构。
* zhihuSpider/pipelines.py: 项目中的pipelines文件，可编写一些pipeline类，用来操作抓取到的item，比如存入数据库，或者保存json文件
* zhihuSpider/settings.py: 项目的设置文件。
* zhihuSpider/spiders/: 放置spider代码的目录

#### 定义Item数据

Item是保存抓取到数据的容器。类似于ORM，可创建一个**scrapy.Item**类，并且定义类型为**scrapy.Field**的类属性来定义一个Item。

分析下知乎用户中一些我们想要抓取的信息，这里假设只抓取用户名，用户ID，可用户的链接。item.py文件如下：

```python
import scrapy


class ZhihuspiderItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    name = scrapy.Field()
    userID = scrapy.Field()
    link = scrapy.Field()
```

#### 编写spider
创建一个spider，需要继承**scrapy.Spider**类，且定义三个属性：

* name: 用于区分Spider，相当于该爬虫的唯一标识
* start_urls: 包含Spider在启动时进行爬取的url列表
* parse()：Spider的一个方法。每一个抓取完的url生成的**Response**对象会作为参数传递给该方法。该方法可以解析返回的数据，提取数据，以及生成需要进一步处理的URL的**Request**对象。

在zhihuSpider/spiders下新建一个peopleSpider.py文件，如下：

```python
import scrapy

from zhihuSpider.items import ZhihuspiderItem

class PeopleSpider(scrapy.Spider):
    name = "zhihu"
    start_urls = [
        'http://www.zhihu.com/people/Douglee'
    ]

    def parse(self, response): 
        item = ZhihuspiderItem()
        item['link'] =  response.url

        return item
```

#### 开始抓取

运行抓取命令scrapy crawl #name, 如下：

```shell
scrapy crawl zhihu
```

可以从shell窗口看到item的信息，如下图：

![pic](/image/scrapy/2.jpg)

### 使用正则和xpath解析数据

上边的parse()方法只有link，没有name和userID，现在来解析这两个属性。

通过观察可以发现，userID可以从url中获取，这里可以定义一个正则，用正则来获取userID。

而name可以从抓取到的页面中用xpth分析出来，我经常使用Chrome浏览器，用Chrome可以很方便的取出来xpath，两个方法：

1. 按F12 使用Chrome的develop tools，选中页面上需要的数据，右键copy xpath，如图：
![pic](/image/scrapy/3.png)

2. 安装一个Xpath helper插件，可以更方便的取出xpath。

这里需添加两个引用，完整的代码如下：

```python
import scrapy

from scrapy.selector import Selector
import re

from zhihuSpider.items import ZhihuspiderItem

class PeopleSpider(scrapy.Spider):
    name = "zhihu"
    start_urls = [
        'http://www.zhihu.com/people/Douglee'
    ]

    def parse(self, response):
        nameXpath = r"//div[@class='title-section ellipsis']/span[@class='name']/text()"
        pattern = r'http://www.zhihu.com/people/([\w+-]+)$'

        item = ZhihuspiderItem()
        item['name'] = Selector(response).xpath(nameXpath).extract()[0]
        item['userID'] = re.search(pattern, response.url).group(1)
        item['link'] =  response.url

        return item
```

抓取的shell窗口信息：
![pic](/image/scrapy/4.jpg)

至此，就完成了一个简单的爬虫。

### References

1. 官方文档：http://scrapy.readthedocs.org/en/latest/
2. 中文文档：https://scrapy-chs.readthedocs.org/zh_CN/0.24/
