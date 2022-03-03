title: 使用Form下载文件
category: 技能
date: 2021-04-14

---



<!--more-->

# 使用Form下载文件

```js
function downLoadFile(options) {
    let config = $.extend(true, { method: 'post' }, options);
    let $iframe = $('<iframe id="down-file-iframe" />');
    let $form = $('<form target="down-file-iframe" method="' + config.method + '" />');
    $form.attr('action', config.url);
    for (let key in config.data) {
      $form.append('<input type="hidden" name="' + key + '" value="' + config.data[key] + '" />');
    }
    $iframe.append($form);
    $(document.body).append($iframe);
    $form[0].submit();
    $iframe.remove();
  }

 downLoadFile({
     url: '/dbManager/fileDownload', // 请求的url
     data: params, // 要发送的数据
 });
```

