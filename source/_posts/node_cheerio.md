title: Node爬虫
category: 技能
date: 2021-04-07

---

node爬虫使用cheerio包，它可以像jquery一样对获取到的html文档进行解析。

<!--more-->

# Node爬虫

node爬虫使用cheerio包，它可以像jquery一样对获取到的html文档进行解析。

```js
// 请求 url - > html（信息）  -> 解析html
const https = require('https');
const cheerio = require('cheerio');
const fs = require('fs');
// 请求 top250
// 浏览器输入一个 url, get
https.get('https://movie.douban.com/top250',function(res){
    // console.log(res);
    // 分段返回的 自己拼接
    let html = '';
    // 有数据产生的时候 拼接
    res.on('data',function(chunk){
        html += chunk;
    })
    // 拼接完成
    res.on('end',function(){
        console.log(html);
        const $ = cheerio.load(html);
        let allFilms = [];
        $('li .item').each(function(){
            // this 循环时 指向当前这个电影
            // 当前这个电影下面的title
            // 相当于this.querySelector 
            const title = $('.title', this).text();
            const star = $('.rating_num',this).text();
            const pic = $('.pic img',this).attr('src');
            // console.log(title,star,pic);
            // 存 数据库
            // 没有数据库存成一个json文件 fs
            allFilms.push({
                title,star,pic
            })
        })
        // 把数组写入json里面
        fs.writeFile('./films.json', JSON.stringify(allFilms),function(err){
            if(!err){
                console.log('文件写入完毕');
            }
        })
        // 图片下载一下
        downloadImage(allFilms);
    })
})

function downloadImage(allFilms) {
    for(let i=0; i<allFilms.length; i++){
        const picUrl = allFilms[i].pic;
        // 请求 -> 拿到内容
        // fs.writeFile('./xx.png','内容')
        https.get(picUrl,function(res){
            res.setEncoding('binary');
            let str = '';
            res.on('data',function(chunk){
                str += chunk;
            })
            res.on('end',function(){
                fs.writeFile(`./images/${i}.png`,str,'binary',function(err){
                    if(!err){
                        console.log(`第${i}张图片下载成功`);
                    }
                })
            })
        })
    }
}
```

但是，只要你多研究下目标网站就会发现，很多接口都是对外暴露的，如果发现有些接口需要登陆验证或者权限问题，那么你可以直接访问他的h5页面，h5页面下的大部分接口都可以直接获取。

```js
/**
 * @Author: 柯军
 * @Date: 2019/10/24 14:13:50
 * @Description: 爬虫获取数据
 */
const BaseSvc = require("../Utils/BaseSvc")

module.exports = class ReptileSvc extends BaseSvc {

  constructor(props) {
    super(props);
  }

  /**
  * @createtime 2019/10/24 14:14:24
  * @author 柯军 <arthaskj@163.com>
  * @param {string} type 类型有 movie  tv
  * @param {number} page_limit 获取的数量
  * @param {number} page_start 数据起始点
  * @return {array} List<{cover:图片,id:id,rate:评分,title:标题,url:链接}>
  * @return {object} {showst：4预售，3购票，globalReleased:全球发行，haspromotionTag：有促销标签，coming:array,movieIds:array,movieList:array<{id,nm:名称，img：图像，rt：日期，信息：showInfo，sc：评分，star：演员，version：类型，wish：希望看}>,stid:string,stids:array,total:number}
  * @desc
  */
  async GetHotMovieOrTv(params) {
    let {type = 'now', tag = '热门', page_limit = 50, page_start = 0} = params;
    let doubanUrl = `https://movie.douban.com/j/search_subjects?type=${type}&tag=${encodeURI(tag)}&page_limit=${page_limit}&page_start=${page_start}`,
      maoyanUrl = 'http://m.maoyan.com/ajax/movieOnInfoList?token=',
      cominfUrl = 'http://m.maoyan.com/ajax/comingList?ci=56&token=&limit=100',
      activeUrl;
    switch (type) {
      case 'now':
        activeUrl = maoyanUrl;
        break;
      case 'coming':
        activeUrl = cominfUrl;
        break;

      default:
        break;
    }

    let f = () => {
      return new Promise((success) => {
        this.HTTPRequest(activeUrl, (error, response, body) => {
          success(body)
        });
      })
    }
    return await f();
  }

  /**
  * @createtime 2019/10/24 14:14:24
  * @author 柯军 <arthaskj@163.com>
  * @param {string}
  * @desc
  */
  async GetMovieDetail(params) {
    let {id} = params;
    if (!id) {
      return {success: false, message: '没有传递id', data: null};
    }
    let f = () => {
      return new Promise((success) => {
        this.HTTPRequest(`http://m.maoyan.com/ajax/detailmovie?movieId=${id}`, (error, response, body) => {
          success(body)
        });
      })
    }
    return await f();
  }
};
```

另外，现在很多网站是动态渲染页面的，直接通过http请求拿到的可能是渲染前的html内容，所以这时候就需要使用`Puppeteer`来进行内容获取。`Puppeteer`是一个提供高级API的node库，能够通过devtool控制headless模式的chrome或者chromium，它可以在headless模式下模拟任何的人为操作。