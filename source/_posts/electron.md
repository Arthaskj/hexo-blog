title: electron开发
category: 技能
date: <文章日期> [YYYY-MM-DD]

---

凡是可以用 JavaScript 来写的应用，最终都会用 JavaScript 来写。——Atwood定律（Jeff Atwood在2007年提出）

<!--more-->

## electron开发

> 凡是可以用 JavaScript 来写的应用，最终都会用 JavaScript 来写。——Atwood定律（Jeff Atwood在2007年提出） 

最初接触到js做桌面端开发使用的是NW.js,但是用起来很别扭，后来electron开始流行，于是开始进行研究。

使用electron开发遇到的最大的难点就是安装electron。。。特别是在win系统下，他的安装耗费了很长时间，上一个让我耗费这么长时间安装的项目还是搭建React Native环境。。。

开始直接使用`npm install -g electron` 命令进行安装，但是出现各种超时，换了各种仓库也不行，然后默默地开启了科学上网，虽然进度条在走了，但是下载速度惨不忍睹。。。最终在win系统下我使用的是直接将网上下载的electron包拷贝到AppData下的对应目录，然后配置环境变量才启动起来。。。

相对于win系统，在MacOs下安装要方便很多，不知道是什么原因~

Electron开发不要太简单，只要会写HTML，就能写客户端，剩下的交给时间慢慢打磨即可，Atom、VSCode、Slack等都是用Electron开发的。，Node.JS虽说不是最终极的优秀中间件，但是目前来看在Desktop这一块还有发挥余热的地方。

![image-20210318105836345](/images/electron.assets/image-20210318105836345.png)

Chromium : 为Electron提供了强大的UI能力，可以不考虑兼容性的情况下，利用强大的Web生态来开发界面

Node.js ：让Electron有了底层的操作能力，比如文件的读写，甚至是集成C++等等操作，并可以使用大量开源的 npm 包来完成开发需求

Native API ：Native API让Electron有了跨平台和桌面端的原生能力，比如说它有统一的原生界面，窗口、托盘这些

理论上说，electron可以将任何前端项目直接打包成桌面软件，要做的就是前端项目的路由处理以及桌面端优化。由于electron打包后使用的`file://`请求文件，所以一定要注意项目的目录配置，否则就会使一堆404报错~

![image-20210318105337331](/images/electron.assets/image-20210318105337331.png)

```js
// main.js

const {
  app,
  Menu,
  BrowserWindow
} = require('electron')
const path = require('path')
const store = new(require('electron-store'))
const sessionCookieStoreKey = 'cookies.mainWindow';
const url = require('url');
// const autoUpdater = require('auto-updater');
const appVersion = require('./package.json').version;
const os = require('os').platform();

process.env['ELECTRON_DISABLE_SECURITY_WARNINGS'] = 'true'

let mainWindow
let isDev = process.env.NODE_ENV !== 'production';
let scriptPath = path.join(__dirname, 'preload.js');
let indexPath = path.join(__dirname, 'dist/index.html');

var updateFeed = 'http://localhost:3000/Updates/Latest';

// if (process.env.NODE_ENV !== 'development') {
//   updateFeed = os === 'darwin' ?
//     'https://mysite.com/updates/latest' :
//     'http://download.mysite.com/releases/win32';
// }

// autoUpdater.setFeedURL(updateFeed + '?v=' + appVersion);
// autoUpdater.quitAndInstall();

function createWindow() {
  // Create the browser window.
  mainWindow = new BrowserWindow({
    width: 1280,
    height: 900,
    webPreferences: {
      // preload: path.join(__dirname, 'preload.js')
      preload: scriptPath
    }
  })

  // and load the index.html of the app.
  // mainWindow.loadFile(indexPath)
  // mainWindow.loadURL('http://192.168.0.149:8052')
  // 并且装载应用的index.html页面,__dirname为当前文件路径
  mainWindow.loadURL(url.format({
    pathname: path.join(__dirname, "./dist/index.html"),
    protocol: 'file:',
    slashes: true
  }));

  // Emitted when the window is closed.
  mainWindow.on('closed', function() {
    mainWindow = null
  })

  let promise = new Promise((resolve) => {
    let cookies = store.get(sessionCookieStoreKey) || [];
    let recoverTimes = cookies.length;
    if (recoverTimes <= 0) {
      //无cookie数据无需恢复现场
      resolve()
      return;
    }
    //恢复cookie现场
    cookies.forEach((cookiesItem) => {
      let {
        secure = false,
        domain = '',
        path = ''
      } = cookiesItem

      mainWindow.webContents.session.cookies
        .set(
          Object.assign(cookiesItem, {
            url: (secure ? 'https://' : 'http://') + domain.replace(/^\./, '') + path
          })
        )
        .then(() => {})
        .catch((e) => {
          console.error({
            message: '恢复cookie失败',
            cookie: cookiesItem,
            errorMessage: e.message,
          })
        })
        .finally(() => {
          recoverTimes--;
          if (recoverTimes <= 0) {
            resolve();
          }
        })
    });
  })

  // 通过store提供的本地化接口持久化cookies数据      2019/12/25 18:07:29      --柯军
  promise.then(() => {
    //监听cookie变化保存cookie现场
    return new Promise((resolve) => {
      let isCookiesChanged = false;
      mainWindow.webContents.session.cookies.on('changed', () => {
        //检测cookies变动事件，标记cookies发生变化
        isCookiesChanged = true;
      });

      //每隔500毫秒检查是否有cookie变动，有变动则进行持久化
      setInterval(() => {
        if (!isCookiesChanged) {
          return;
        }
        mainWindow.webContents.session.cookies.get({})
          .then((cookies) => {
            store.set(sessionCookieStoreKey, cookies);
          })
          .catch((error) => {
            console.log({
              error
            })
          })
          .finally(() => {
            isCookiesChanged = false;
          })
      }, 500);

      resolve();
    })
  })

}

app.on('ready', createWindow)

app.on('window-all-closed', function() {
  if (process.platform !== 'darwin') app.quit()
})

app.on('activate', function() {
  if (mainWindow === null) createWindow()
})

//设置菜单
let dockMenu = Menu.buildFromTemplate([
  {
    label: '首页', click: function () {
      mainWindow.loadFile(indexPath)
    }
  },
  {label: '帮助',click(){
      mainWindow.webContents.openDevTools();
    }}
]);
Menu.setApplicationMenu(dockMenu);
// Menu.setApplicationMenu(null);

```

