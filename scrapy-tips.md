# Scrapy Tips


运行时修改爬虫设置，即覆盖 Spider 类中的 set_crawler 方法：

```python
def set_crawler(self, crawler):
        '''运行时修改爬虫设置'''

        super(QqBjSpider, self).set_crawler(crawler)
        crawlerHeaders = crawler.settings.get('DEFAULT_REQUEST_HEADERS')
        crawlerHeaders.update({'Referer': 'http://roll.house.qq.com/'})
        crawler.settings.set('DEFAULT_REQUEST_HEADERS',crawlerHeaders)
```