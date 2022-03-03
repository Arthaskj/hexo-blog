title: 浏览量统计接口
category: 技能
date: <文章日期> [YYYY-MM-DD]
index_img: /images/views.assets/image-20210317161718429.png

---

自己的博客上线后一直想添加一个浏览量记录功能，网上找了一款工具`不蒜子`，但是可能因为使用的人太多，导致接口请求和文件请求很慢，于是看了看它的源码，决定自己撸一个。

<!--more-->

## 浏览量统计接口

自己的博客上线后一直想添加一个浏览量记录功能，网上找了一款工具`不蒜子`，但是可能因为使用的人太多，导致接口请求和文件请求很慢，于是看了看它的源码，决定自己撸一个。

首先是接口，很简单的一个计算过程，将访问页面的host和hash作为唯一键，页面初始化时调用一次接口，如果数据库里不存在则创建一条新的数据，数据库里存在则更新数据，然后将结果返回就行了。

```js
async UpdateStorage(params) {
    const { host, hash } = params;
    const data = await this.ctx.model.Storage.findOne({ host, hash });
    let d = data;
    if (!data) {
      d = { ...params, sv: 0, pv: 0 };
    }
    let { sv = 0, pv = 0 } = d;
    sv++; pv++;

    const dd = { host, hash, sv, pv };

    // 没有记录则添加新纪录，否则更新记录
    if (!data) {
      dd.updateTime = this.ctx.helper.dateFormat(new Date(), 'YYYY/mm/dd HH:MM:SS');
      await this.ctx.model.Storage.create(dd);
    } else {
      await this.ctx.model.Storage.updateOne({ host, hash }, dd);
    }
    const datas = await this.ctx.model.Storage.find({ host });
    dd.sv = datas.map(x => x.pv).reduce((a, b) => a + b);
    return dd;
  }
```

![image-20210317161718429](/images/views.assets/image-20210317161718429.png)

前端我使用的是`Docsify`框架，使用它提供的api，我们直接在页面加载时进行请求就好了。

![image-20210317161319897](/images/views.assets/image-20210317161319897.png)

看看效果~

![image-20210317161649308](/images/views.assets/image-20210317161649308.png)