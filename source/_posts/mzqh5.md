title: 一个WebApp项目(Vue、Vant)
category: 技能
tags: vue,vant,h5,webapp
date: 2021-04-09
index_img: /images/mzqh5.assets/image-20210316153447696.png

---

一个WebApp项目(Vue、Vant)


<!--more-->

## 一个WebApp项目(Vue、Vant)

> 项目使用到Vue框架、Vant-ui框架以及TypeScript来实现

### 一、用户验证

前端这边进行登录与注册页面的过滤，非登录注册页面在没有检测到token时进行页面跳转

![image-20210316153447696](/images/mzqh5.assets/image-20210316153447696.png)

跳转登录与注册界面后进行登录操作，登录成功后将token与用户信息保存在localStorage中

![image-20210316153607111](/images/mzqh5.assets/image-20210316153607111.png)

之后所有的请求都将携带token信息,如果没有token或者token验证失败时页面跳转到登录界面

```js
const request = (config: IRequestConfig) => {
  let token = localStorage.getItem('token');
  const userId = localStorage.getItem('userId');
  if (!userId) {
    token = '';
  }
  config.headers = { ...(config.headers || {}), Authorization: 'Bearer ' + token, userId, };
	return client.request(config).then((response: AxiosResponse) => {
		const { data, message, success } = response.data;
		if (success) {
			return data
		} else {
			console.error(message);
		}
			// return response.data;
		})
		.catch((error: AxiosError) => {
      switch((error.response || {}).status){
        case 401:
          localStorage.removeItem('token')
          localStorage.removeItem('userId')
          router.push('/signUp');
          console.error('登录信息已失效，请重新登录');
          break;
        case 403:
          localStorage.removeItem('token')
          localStorage.removeItem('userId')
          router.push('/signUp');
          console.error('用户密码不正确，请检查');
          break;
        case 500:
          Notify({ type: 'warning', message: (error.response || {}).data.message || '接口异常!' });
          break;
        case 501:
          Notify({ type: 'danger', message: (error.response || {}).data.message || '接口异常!' });
          break;
        default:break;
      }
			return Promise.reject(error);
		});
}
```

![image-20210316154556606](/images/mzqh5.assets/image-20210316154556606.png)

![image-20210316154612075](/images/mzqh5.assets/image-20210316154612075.png)

### 二、待办功能

没啥说的，简简单单一待办~

![image-20210316154132360](/images/mzqh5.assets/image-20210316154132360.png)

![image-20210316154159691](/images/mzqh5.assets/image-20210316154159691.png)

![image-20210316154204235](/images/mzqh5.assets/image-20210316154204235.png)

### 三、基金功能

>  你不理财，财不理你，你若理财，财远离你。

于是为了更好的成为一个富翁(韭菜)，我给自己开发了一些功能，首先就是你可以添加自己感兴趣的基金，然后在这个界面你就可以看到他们的涨跌状态，同时通过设置最大值最小值，可以在基金净价到达最大值最小值时发送邮件对你进行提醒。

![image-20210316154407318](/images/mzqh5.assets/image-20210316154407318.png)

不需要的基金也可以进行删除，对感兴趣的基金可以收藏，那么他们就会展示在考前的位置

![image-20210316154413436](/images/mzqh5.assets/image-20210316154413436.png)

同时你可以随时了解大盘行情~~~

![image-20210316154418271](/images/mzqh5.assets/image-20210316154418271.png)

![image-20210316154427649](/images/mzqh5.assets/image-20210316154427649.png)

同时你可以对你收藏的基金每天进行涨跌记录

![image-20210316154437108](/images/mzqh5.assets/image-20210316154437108.png)

![image-20210316155110214](/images/mzqh5.assets/image-20210316155110214.png)

然后你做了这么多，依然改变不了你是个韭菜的事实。。。。。。。。。。。

![image-20210316154444432](/images/mzqh5.assets/image-20210316154444432.png)

> 2021年开年真就跌跌跌。。。心碎的声音。。。

### 四、后台实现

后端使用Egg框架进行开发，使用mongoDB进行数据存储

![image-20210316155517373](/images/mzqh5.assets/image-20210316155517373.png)

通过Egg框架的定时器功能开发了邮件提醒以及基金信息更新的功能

```js
const Subscription = require('egg').Subscription;
const sendMail = require('../lib/mail');

class UpdateCache extends Subscription {
  // 通过 schedule 属性来设置定时任务的执行间隔等配置
  // *    *    *    *    *    *
  // ┬    ┬    ┬    ┬    ┬    ┬
  // │    │    │    │    │    |
  // │    │    │    │    │    └ day of week (0 - 7) (0 or 7 is Sun)
  // │    │    │    │    └───── month (1 - 12)
  // │    │    │    └────────── day of month (1 - 31)
  // │    │    └─────────────── hour (0 - 23)
  // │    └──────────────────── minute (0 - 59)
  // └───────────────────────── second (0 - 59, optional)
  static get schedule() {
    return {
      cron: '0 0,30 9,10,11,13,14,15 * * 1,2,3,4,5 ',
      // interval: '1h', // 1 分钟间隔
      type: 'all', // 指定所有的 worker 都需要执行
      immediate: true, // 应用启动并ready后立刻执行一次
    };
  }

  // subscribe 是真正定时任务执行时被运行的函数
  async subscribe() {
    const arr = [];
    const list = await this.ctx.service.fund.getAll({ userId: '******' });
    let index = 0;
    for (const item of list) {
      const { code, min, max } = item;
      const fundInfo = await this.ctx.service.fund.getFundInfo({ code });
      if (!item.name) {
        item.name = fundInfo.name;
        this.ctx.service.fund.updateFundName(item);
      }

      if (Number(fundInfo.gsz) <= Number(min)) {
        arr.push(this.getTpl({ data: fundInfo, min, max, type: 'buy' }));
      }
      if (Number(fundInfo.gsz) >= Number(max)) {
        arr.push(this.getTpl({ data: fundInfo, min, max, type: 'sell' }));
      }

      if (index === list.length - 1) {
        console.log('schedule', new Date().getTime(), arr.length);
        await sendMail([ 'arthaskj@163.com' ], `基金买入/卖出提醒(${arr.length})`, arr.join(''));
        index = 0;
      }
      index++;
    }
  }

  getTpl({ data, min, max, type }) {
    const { gsz, fundcode, name, gztime, jzrq, dwjz } = data;
    const color = type === 'buy' ? '#34a853' : '#f5222d';
    return `<div style="margin-bottom: 20px;border: 5px solid ${color};padding: 10px;">
              <span style="font-size: 24px;font-weight: bold;color:#1890ff;">名称:</span>
              <br/>
              <span style="color:${color};">${name}【${fundcode}】</span>

              <br />
              <span style="font-size: 24px;font-weight: bold;color:#1890ff;">最大值:</span>
              <br/>
              <span>${max || '--'}</span>
              <br />
              <span style="font-size: 24px;font-weight: bold;color:#1890ff;">最小值:</span>
              <br/>
              <span>${min || '--'}</span>
              <br />
              <span style="font-size: 24px;font-weight: bold;color:#1890ff;">估算值:</span>
              <br/>
              <span style="color:${color};">${gsz}</span>

              <br />
              <span style="font-size: 24px;font-weight: bold;color:#1890ff;">净值:</span>
              <br/>
              <span>${dwjz}</span>

               <br />
              <span style="font-size: 24px;font-weight: bold;color:#1890ff;">估值日期:</span>
              <br/>
              <span>${gztime}</span>

               <br />
              <span style="font-size: 24px;font-weight: bold;color:#1890ff;">净值日期:</span>
              <br/>
              <span>${jzrq}</span>
              <br />
              <br />
              <img style="width: 100%;height: auto;margin: 0 auto;" src="https://j4.dfcfw.com/charts/pic6/${fundcode}.png">
            </div>`;
  }
}

module.exports = UpdateCache;

```

![image-20210316155644114](/images/mzqh5.assets/image-20210316155644114.png)


### 数据获取
一开始我使用的是`puppeteer`框架进行数据抓取，本地调试没有任何问题，成功获取到数据。BUT！！！放到自己的服务器上就不行了，因为自己的linux服务器配置太低，以及各种版本问题，导致`Chromium`根本驱动不起来。。。
最后经过分析基金网站，发现他们提供的接口完全可以直接调用，进行字符串解析就行了。。。一切搞定~