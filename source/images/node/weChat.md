## 验证微信公众号

```js

  /**
   * 开发者通过检验signature对请求进行校验（下面有校验方式）。
   * 若确认此次GET请求来自微信服务器，请原样返回echostr参数内容，则接入生效，成为开发者成功，否则接入失败。加密/校验流程如下：
   * 1）将token、timestamp、nonce三个参数进行字典序排序
   * 2）将三个参数字符串拼接成一个字符串进行sha1加密
   * 3）开发者获得加密后的字符串可与signature对比，标识该请求来源于微信
   */
  async CheckWeChatServer(params = {}) {
    // 1.获取微信服务器Get请求的参数 signature、timestamp、nonce、echostr
    const { signature, timestamp, nonce, echostr, xml } = params;


    // 2.将token、timestamp、nonce三个参数进行字典序排序
     const array = [ config.wechat.token, timestamp, nonce ];
      array.sort();
    // 3.将三个参数字符串拼接成一个字符串进行sha1加密
     const tempStr = array.join(''),
      hashCode = crypto.createHash('sha1'), // 创建加密类型
      resultCode = hashCode.update(tempStr, 'utf8')
        .digest('hex'); // 对传入的字符串进行加密

    const msgtype = xml && xml.msgtype[0].toString();

    // 4.开发者获得加密后的字符串可与signature对比，标识该请求来源于微信
    if (resultCode === signature) {
      return echostr;
    }
    return 'mismatch';
  }
```

