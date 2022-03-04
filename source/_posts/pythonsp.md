title: python爬虫网页乱码问题
category: 技能
tags: python,爬虫
date: 2019-08-14
index_img: 

---

python爬虫网页乱码问题

<!--more-->

<!--
 * @Author: 柯军
 * @Date: 2019-08-14 18:49:15
 * @Description: 
 -->
### python爬虫网页乱码问题

```
url = 'http://m.qulaoshi.com/zhongban/yinyue/11273/'
head = {
    "User-Agent": "Mozilla/5.0 (Windows NT 6.1; WOW64) Firefox/21.0",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8",
    "Accept-Encoding": "gzip, deflate, br",
    "Accept-Language": "en-US,zh-CN,zh;q=0.9"
}
resp=requests.get(url, headers=head,timeout=15)
resp.encoding=resp.apparent_encoding   #设置解码方式
html_source=resp.text      #这里会用设置的解码方式解码
print(html_source)	      #正确解码后直接打印也不会出现乱码
resp.close()
# if html_source:
# #将html写文件时设置文件编码方式，因为和html里设置的编码格式一样，所以直接打开就不会出现乱码了
#     with open('test.htm', mode='a+', encoding=resp.apparent_encoding) as file:
#         file.write(html_source)
```
[链接地址](https://blog.csdn.net/qq_33440662/article/details/82787301)