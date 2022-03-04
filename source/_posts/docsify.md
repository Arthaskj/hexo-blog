title: Docsify博客系统
category: 技能
tags: docsify,博客
date: 2021-03-18

---

docsify是一个博客框架，相比于其他博客框架，他是在运行时对md文件进行处理，所以更改过的文件马上就能被渲染，十分方便，结合[自动部署ci系统](src/md/ci/gitee)，只需要进行push操作就可以进行博客的实时部署。
对于如何使用Docsify可以直接到官网进行查询[Docsify快速开始](https://docsify.js.org/#/zh-cn/quickstart)，这里我只进行相关配置的介绍。

<!--more-->

## Docsify博客系统

docsify是一个博客框架，相比于其他博客框架，他是在运行时对md文件进行处理，所以更改过的文件马上就能被渲染，十分方便，结合[自动部署ci系统](src/md/ci/gitee)，只需要进行push操作就可以进行博客的实时部署。
对于如何使用Docsify可以直接到官网进行查询[Docsify快速开始](https://docsify.js.org/#/zh-cn/quickstart)，这里我只进行相关配置的介绍。

# [配置项](https://docsify.js.org/#/zh-cn/configuration?id=配置项)

你可以配置在 `window.$docsify` 里。

```html
<script>
  window.$docsify = {
    repo: 'docsifyjs/docsify',
    maxLevel: 3,
    coverpage: true,
  };
</script>
```

## [el](https://docsify.js.org/#/zh-cn/configuration?id=el)

- 类型：`String`
- 默认值：`#app`

docsify 初始化的挂载元素，可以是一个 CSS 选择器，默认为 `#app` 如果不存在就直接绑定在 `body` 上。

```js
window.$docsify = {
  el: '#app',
};
```

## [repo](https://docsify.js.org/#/zh-cn/configuration?id=repo)

- 类型：`String`
- 默认值: `null`

配置仓库地址或者 `username/repo` 的字符串，会在页面右上角渲染一个 [GitHub Corner](http://tholman.com/github-corners/) 挂件。

```js
window.$docsify = {
  repo: 'docsifyjs/docsify',
  // or
  repo: 'https://github.com/docsifyjs/docsify/',
};
```

## [maxLevel](https://docsify.js.org/#/zh-cn/configuration?id=maxlevel)

- 类型：`Number`
- 默认值: `6`

默认情况下会抓取文档中所有标题渲染成目录，可配置最大支持渲染的标题层级。

```js
window.$docsify = {
  maxLevel: 4,
};
```

## [loadNavbar](https://docsify.js.org/#/zh-cn/configuration?id=loadnavbar)

- 类型：`Boolean|String`
- 默认值: `false`

加载自定义导航栏，参考[定制导航栏](https://docsify.js.org/#/zh-cn/custom-navbar) 了解用法。设置为 `true` 后会加载 `_navbar.md` 文件，也可以自定义加载的文件名。

```js
window.$docsify = {
  // 加载 _navbar.md
  loadNavbar: true,

  // 加载 nav.md
  loadNavbar: 'nav.md',
};
```

## [loadSidebar](https://docsify.js.org/#/zh-cn/configuration?id=loadsidebar)

- 类型：`Boolean|String`
- 默认值: `false`

加载自定义侧边栏，参考[多页文档](https://docsify.js.org/#/zh-cn/more-pages)。设置为 `true` 后会加载 `_sidebar.md` 文件，也可以自定义加载的文件名。

```js
window.$docsify = {
  // 加载 _sidebar.md
  loadSidebar: true,

  // 加载 summary.md
  loadSidebar: 'summary.md',
};
```

## [hideSidebar](https://docsify.js.org/#/zh-cn/configuration?id=hidesidebar)

- 类型 : `Boolean`
- 默认值: `true`

这个选项用来完全隐藏侧边栏，侧边栏的任何内容都不会被渲染。

```js
window.$docsify = {
  hideSidebar: true,
};
```

## [subMaxLevel](https://docsify.js.org/#/zh-cn/configuration?id=submaxlevel)

- 类型：`Number`
- 默认值: `0`

自定义侧边栏后默认不会再生成目录，你也可以通过设置生成目录的最大层级开启这个功能。

```js
window.$docsify = {
  subMaxLevel: 2,
};
```

## [auto2top](https://docsify.js.org/#/zh-cn/configuration?id=auto2top)

- 类型：`Boolean`
- 默认值: `false`

切换页面后是否自动跳转到页面顶部。

```js
window.$docsify = {
  auto2top: true,
};
```

## [homepage](https://docsify.js.org/#/zh-cn/configuration?id=homepage)

- 类型：`String`
- 默认值: `README.md`

设置首页文件加载路径。适合不想将 `README.md` 作为入口文件渲染，或者是文档存放在其他位置的情况使用。

```js
window.$docsify = {
  // 入口文件改为 /home.md
  homepage: 'home.md',

  // 文档和仓库根目录下的 README.md 内容一致
  homepage:
    'https://raw.githubusercontent.com/docsifyjs/docsify/master/README.md',
};
```

## [basePath](https://docsify.js.org/#/zh-cn/configuration?id=basepath)

- 类型：`String`

文档加载的根路径，可以是二级路径或者是其他域名的路径。

```js
window.$docsify = {
  basePath: '/path/',

  // 直接渲染其他域名的文档
  basePath: 'https://docsify.js.org/',

  // 甚至直接渲染其他仓库
  basePath:
    'https://raw.githubusercontent.com/ryanmcdermott/clean-code-javascript/master/',
};
```

## [relativePath](https://docsify.js.org/#/zh-cn/configuration?id=relativepath)

- 类型: `Boolean`
- 默认值: `false`

如果该选项设为 **true** ，那么链接会使用相对路径。

例如，像这样的目录结构：

```text
.
└── docs
    ├── README.md
    ├── guide.md
    └── zh-cn
        ├── README.md
        ├── guide.md
        └── config
            └── example.md
```

如果 **启用** 了相对路径，当前链接是 `http://domain.com/zh-cn/README` ，对应的链接会解析为：

```text
guide.md              => http://domain.com/zh-cn/guide
config/example.md     => http://domain.com/zh-cn/config/example
../README.md          => http://domain.com/README
/README.md            => http://domain.com/README
window.$docsify = {
  // 启用相对路径
  relativePath: true,

  // 禁用相对路径（默认值）
  relativePath: false,
};
```

## [coverpage](https://docsify.js.org/#/zh-cn/configuration?id=coverpage)

- 类型：`Boolean|String`
- 默认值: `false`

启用[封面页](https://docsify.js.org/#/zh-cn/cover)。开启后是加载 `_coverpage.md` 文件，也可以自定义文件名。

```js
window.$docsify = {
  coverpage: true,

  // 自定义文件名
  coverpage: 'cover.md',

  // 多个封面页
  coverpage: ['/', '/zh-cn/'],

  // 多个封面页，并指定文件名
  coverpage: {
    '/': 'cover.md',
    '/zh-cn/': 'cover.md',
  },
};
```

## [logo](https://docsify.js.org/#/zh-cn/configuration?id=logo)

- 类型: `String`

在侧边栏中出现的网站图标，你可以使用`CSS`来更改大小

```js
window.$docsify = {
  logo: '/_media/icon.svg',
};
```

## [name](https://docsify.js.org/#/zh-cn/configuration?id=name)

- 类型：`String`

文档标题，会显示在侧边栏顶部。

```js
window.$docsify = {
  name: 'docsify',
};
```

name 项也可以包含自定义 HTML 代码来方便地定制。

```js
window.$docsify = {
  name: '<span>docsify</span>',
};
```

## [nameLink](https://docsify.js.org/#/zh-cn/configuration?id=namelink)

- 类型：`String`
- 默认值：`window.location.pathname`

点击文档标题后跳转的链接地址。

```js
window.$docsify = {
  nameLink: '/',

  // 按照路由切换
  nameLink: {
    '/zh-cn/': '/zh-cn/',
    '/': '/',
  },
};
```

## [markdown](https://docsify.js.org/#/zh-cn/configuration?id=markdown)

- 类型: `Object|Function`

参考 [Markdown 配置](https://docsify.js.org/#/zh-cn/markdown)。

```js
window.$docsify = {
  // object
  markdown: {
    smartypants: true,
    renderer: {
      link: function() {
        // ...
      },
    },
  },

  // function
  markdown: function(marked, renderer) {
    // ...
    return marked;
  },
};
```

## [themeColor](https://docsify.js.org/#/zh-cn/configuration?id=themecolor)

- 类型：`String`

替换主题色。利用 [CSS3 支持变量](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables)的特性，对于老的浏览器有 polyfill 处理。

```js
window.$docsify = {
  themeColor: '#3F51B5'
};
```

## [alias](https://docsify.js.org/#/zh-cn/configuration?id=alias)

- 类型：`Object`

定义路由别名，可以更自由的定义路由规则。 支持正则。

```js
window.$docsify = {
  alias: {
    '/foo/(.*)': '/bar/$1', // supports regexp
    '/zh-cn/changelog': '/changelog',
    '/changelog':
      'https://raw.githubusercontent.com/docsifyjs/docsify/master/CHANGELOG',
    '/.*/_sidebar.md': '/_sidebar.md', // See #301
  },
};
```

## [autoHeader](https://docsify.js.org/#/zh-cn/configuration?id=autoheader)

- 类型：`Boolean`

同时设置 `loadSidebar` 和 `autoHeader` 后，可以根据 `_sidebar.md` 的内容自动为每个页面增加标题。[#78](https://github.com/docsifyjs/docsify/issues/78)

```js
window.$docsify = {
  loadSidebar: true,
  autoHeader: true,
};
```

## [executeScript](https://docsify.js.org/#/zh-cn/configuration?id=executescript)

- 类型：`Boolean`

执行文档里的 script 标签里的脚本，只执行第一个 script ([demo](https://docsify.js.org/#/zh-cn/themes))。 如果 Vue 存在，则自动开启。

```js
window.$docsify = {
  executeScript: true,
};
## This is test

<script>
  console.log(2333)
</script>
```

注意如果执行的是一个外链脚本，比如 jsfiddle 的内嵌 demo，请确保引入 [external-script](https://docsify.js.org/#/plugins?id=外链脚本-external-script) 插件。

## [noEmoji](https://docsify.js.org/#/zh-cn/configuration?id=noemoji)

- 类型: `Boolean`

禁用 emoji 解析。

```js
window.$docsify = {
  noEmoji: true,
};
```

如果该选项设为 `false` ，但是你不想解析一些特别的表情符，[参考这里](https://github.com/docsifyjs/docsify/issues/742#issuecomment-586313143)

## [mergeNavbar](https://docsify.js.org/#/zh-cn/configuration?id=mergenavbar)

- 类型: `Boolean`

小屏设备下合并导航栏到侧边栏。

```js
window.$docsify = {
  mergeNavbar: true,
};
```

## [formatUpdated](https://docsify.js.org/#/zh-cn/configuration?id=formatupdated)

- 类型: `String|Function`

我们可以通过 **{docsify-updated}** 变量显示文档更新日期. 并且通过 `formatUpdated`配置日期格式。参考 https://github.com/lukeed/tinydate#patterns

```js
window.$docsify = {
  formatUpdated: '{MM}/{DD} {HH}:{mm}',

  formatUpdated: function(time) {
    // ...

    return time;
  },
};
```

## [externalLinkTarget](https://docsify.js.org/#/zh-cn/configuration?id=externallinktarget)

- 类型: `String`
- 默认: `_blank`

外部链接的打开方式。默认为 `_blank` （在新窗口或者标签页中打开）

```js
window.$docsify = {
  externalLinkTarget: '_self', // default: '_blank'
};
```

## [cornerExternalLinkTarget](https://docsify.js.org/#/zh-cn/configuration?id=cornerexternallinktarget)

- 类型:`String`
- 默认值:`_blank`

右上角链接的打开方式。默认为 `_blank` （在新窗口或者标签页中打开）

```js
window.$docsify = {
  cornerExternalLinkTarget: '_self', // default: '_blank'
};
```

## [externalLinkRel](https://docsify.js.org/#/zh-cn/configuration?id=externallinkrel)

- 类型: `String`
- 默认值: `noopener`

默认为 `noopener` (no opener) 可以防止新打开的外部页面（当 [externalLinkTarget](https://docsify.js.org/#/zh-cn/configuration?id=externallinktarget) 设为 `_blank` 时）能够控制我们的页面，没有设为 `_blank` 的话就不需要设置这个选项了。

```js
window.$docsify = {
  externalLinkRel: '', // default: 'noopener'
};
```

## [routerMode](https://docsify.js.org/#/zh-cn/configuration?id=routermode)

- 类型: `String`
- 默认: `hash`

```js
window.$docsify = {
  routerMode: 'history', // default: 'hash'
};
```

## [crossOriginLinks](https://docsify.js.org/#/zh-cn/configuration?id=crossoriginlinks)

- type: `Array`

当设置了`routerMode:'history'`时，你可能会面临跨域的问题，参见 [#1379](https://github.com/docsifyjs/docsify/issues/1379) 。在 Markdown 内容中，有一个简单的方法可以解决，参见[helpers](https://docsify.js.org/#/zh-cn/helpers) 中的跨域链接。

```js
window.$docsify = {
  crossOriginLinks: ['https://example.com/cross-origin-link'],
};
```

## [noCompileLinks](https://docsify.js.org/#/zh-cn/configuration?id=nocompilelinks)

- 类型: `Array`

有时我们不希望 docsify 处理我们的链接。 参考 [#203](https://github.com/docsifyjs/docsify/issues/203)

```js
window.$docsify = {
  noCompileLinks: ['/foo', '/bar/.*'],
};
```

## [onlyCover](https://docsify.js.org/#/zh-cn/configuration?id=onlycover)

- 类型: `Boolean`

只在访问主页时加载封面。

```js
window.$docsify = {
  onlyCover: false,
};
```

## [requestHeaders](https://docsify.js.org/#/zh-cn/configuration?id=requestheaders)

- 类型: `Object`

设置请求资源的请求头。

```js
window.$docsify = {
  requestHeaders: {
    'x-token': 'xxx',
  },
};
```

例如设置缓存

```js
window.$docsify = {
  requestHeaders: {
    'cache-control': 'max-age=600',
  },
};
```

## [ext](https://docsify.js.org/#/zh-cn/configuration?id=ext)

- 类型: `String`

资源的文件扩展名。

```js
window.$docsify = {
  ext: '.md',
};
```

## [fallbackLanguages](https://docsify.js.org/#/zh-cn/configuration?id=fallbacklanguages)

- 类型: `Array<string>`

一个语言列表。在浏览这个列表中的语言的翻译文档时都会在请求到一个对应语言的翻译文档，不存在时显示默认语言的同名文档

Example:

- 尝试访问`/de/overview`，如果存在则显示
- 如果不存在则尝试`/overview`（取决于默认语言），如果存在即显示
- 如果也不存在，显示404页面

```js
window.$docsify = {
  fallbackLanguages: ['fr', 'de'],
};
```

## [notFoundPage](https://docsify.js.org/#/zh-cn/configuration?id=notfoundpage)

- 类型: `Boolean` | `String` | `Object`

在找不到指定页面时加载`_404.md`:

```js
window.$docsify = {
  notFoundPage: true,
};
```

加载自定义404页面:

```js
window.$docsify = {
  notFoundPage: 'my404.md',
};
```

加载正确的本地化过的404页面:

```js
window.$docsify = {
  notFoundPage: {
    '/': '_404.md',
    '/de': 'de/_404.md',
  },
};
```

> 注意: 配置过`fallbackLanguages`这个选项的页面与这个选项`notFoundPage`冲突。

## [topMargin](https://docsify.js.org/#/zh-cn/configuration?id=topmargin)

- 类型: `Number`
- 默认值: `0`

让你的内容页在滚动到指定的锚点时，距离页面顶部有一定空间。当你使用 `固定页头` 布局时这个选项很有用，可以让你的锚点对齐标题栏。

```js
window.$docsify = {
  topMargin: 90, // default: 0
};
```



> 官方说是兼容Vue，但是兼容性并不好，所以Vue相关可以不用看了

### index.html文件

```html
<!--
 * @Author: 柯军
 * @Date: 2017-10-26 16:15:00
 * @Description:
 -->
<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="UTF-8">
  <title>Arthaskj-Blog</title>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="keywords" content="arthaskj,arthas,kejun,blog,kj,jke">
  <meta name="description" content="arthaskj blog">
  <link rel="icon" href="./assets/brother_l.jpg" type="image/x-icon" />
  <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
  <link rel="stylesheet" href="lib/css/vue.css">
    
    <!-- 这些功能引入的话会大大影响加载速度，非必要不需要添加 -->
  <!-- 做图引入 -->
<!--  <link rel="stylesheet" href="lib/css/mermaid.min.css">-->
<!--  <script src="lib/js/mermaid.min.js"></script>-->
  <!-- 公式引入 -->
<!--  <link href="lib/css/katex.min.css" rel="stylesheet">-->
<!--  <script src="lib/js/katex.min.js"></script>-->

<!-- <link rel="stylesheet" href="lib/css/gitalk.css"> -->



</head>
<script type="module" src="./index.js"></script>

<body>
  <div id="app">正在加载，请稍后。。。</div>
  <script>
    window.$docsify = {
      search: {
        maxAge: 86400000, // 过期时间，单位毫秒，默认一天
        paths: 'auto',
        placeholder: '输入查询内容...',
        noData: '未找到结果!',
        depth: 6          // 搜索标题的最大程级, 1 - 6
      },
      // 代码块点击复制
      copyCode: {
        buttonText : '复制',
        errorText  : '复制失败',
        successText: '复制成功'
      },
      logo: './assets/trash_m.jpg', //侧边栏出现的图标
      name: 'Arthaskj',
      repo: 'https://github.com/****/gitblog/', //在右上角添加github图标

      loadSidebar: true, //显示侧边栏
      alias: {
        '/.*/_sidebar.md': '/_sidebar.md',
        '/.*/_navbar.md': '/_navbar.md'
      },
      subMaxLevel: 2,
      autoHeader: true, //自动显示标题

      maxLevel: 4, //目录最多展示4级
      coverpage: './_coverpage.md', //展示封面
      // loadNavbar: true,//加载导航栏
      // mergeNavbar: true,//小屏幕导航栏移动到侧边栏
      auto2top: true, //跳转页面自动返回顶部
      notFoundPage: 'my404.md', //自定义404页面
      requestHeaders: {
        'cache-control': 'max-age=600',
      },
		
      formatUpdated: '{YYYY}/{MM}/{DD} {HH}:{mm}:{ss}', //格式化时间{docsify-updated}
      plugins: [
        function(hook) {
            // 底部版权和备案信息添加
          var footer = [
            '<hr/>',
            '<footer style="text-align: center;">',
            `<span><a href="https://github.com/***/gitblog/" target="_blank">KJ-Blog</a> &copy;2017-${new Date().getFullYear()}</span><br />`,
            '<div><a href="http://www.beian.miit.gov.cn">***</a></div>'+
            '</footer>'
          ].join('')

          hook.afterEach(function(html) {
            return html + footer
          })
        },
        function(hook) {
            // 顶部信息添加
          var content = [
            `> 作&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;者: Arthaskj`,
            `<br />`,
            `更新时间: {docsify-updated}`,
            '<br /><span id="busuanzi_container_page_pv">当前页访问量: <span id="busuanzi_value_page_pv" style="font-weight: bold;"></span>次 &nbsp;   </span>',
            '<br /><span id="busuanzi_container_site_pv">本站总访问量: <span id="busuanzi_value_site_pv" style="font-weight: bold;"></span>次 &nbsp;   </span>',
            '\n',
          ].join('');

          hook.beforeEach(function(html) {
            return content + html;
          })
        },
        function(hook) {
            // 浏览量记录
          hook.doneEach(function() {
            fetch(`/api/controller/entil?host=${window.location.host}&hash=${window.location.hash.replace('#','arthas')}`)
            .then(res => res.json()).then(res => {
              const { pv, sv } = res.data;
              document.querySelector('#busuanzi_value_page_pv').innerHTML = pv;
              document.querySelector('#busuanzi_value_site_pv').innerHTML = sv;
            })
          })
        }
      ],
    }
  </script>
  <!-- vue引入 -->
<!--  <script src="lib/js/vue.js"></script>-->
<script src="//cdn.jsdelivr.net/npm/vue@2/dist/vue.min.js"></script>
  <!-- <script src="//unpkg.com/docsify"></script> -->
  <script src="lib/js/docsify.min.js"></script>
  <script src="lib/js/emoji.js"></script>
  <!--搜索功能-->
  <script src="lib/js/search.js"></script>
  <!--复制代码-->
  <script src="lib/js/docsify-copy-code.min.js"></script>
  <!--分页导航-->
  <script src="lib/js/docsify-pagination.min.js"></script>

<!-- 本来打算使用gitalk来作为博客的评论系统，但是国内效果并不是很好~所以最后可能会自己写一个组件 -->
<!-- <script src="lib/js/gitalk.min.js"></script>
<script src="lib/js/gitalklib.min.js"></script>
<script>
  const gitalk = new Gitalk({
    clientID: '***',
    clientSecret: '***',
    repo: 'gitblog',
    owner: 'Arthaskj',
    admin: ['Arthaskj'],
    // facebook-like distraction free mode
    distractionFreeMode: true
  })
</script> -->
</body>

</html>

```





