## 验证微信小程序获取openid

```js
/**
   * @createTime 2020-07-02 13:35:37
   * @description 微信小程序验证，返回openid
   */
  async GetWeApi(params = {}) {
    const { code } = params,
     { appId, secret, grant_type } = config.wechatP,
    // eslint-disable-next-line max-len
     result = await this.ctx.curl(`https://api.weixin.qq.com/sns/jscode2session?appid=${appId}&secret=${secret}&js_code=${code}&grant_type=${grant_type}`,
      {
        method: 'GET',
        dataType: 'json',
      });
    return result.data;
  }
```

