title: 针对Jquery老项目使用gulp进行打包
category: 技能
tags: gulp,jquery
date: 2021-03-19
index_img: /images/gulp.assets/image-20210319153457812.png

---

目前流行的框架都使用webpack进行打包，但是由于webpack的特性，它并不适用于使用jquery或者第三方框架写的前端项目，导致一些老项目里不能使用Es6、less、scss等新技术。但是gulp却可以让老项目起死回生。

<!--more-->


# 针对Jquery老项目使用gulp进行打包

目前流行的框架都使用webpack进行打包，但是由于webpack的特性，它并不适用于使用jquery或者第三方框架写的前端项目，导致一些老项目里不能使用Es6、less、scss等新技术。但是gulp却可以让老项目起死回生。

先介绍下背景，公司开发的管理系统使用的是jquery以及一个根据ExtJs魔改后的前端框架BoxJs，以下简称Box。Box的路由是根据文件夹目录来查询的，所以浏览器看到的hash就是该文件在项目中的位置，一旦更改目录将导致页面无法访问。

![image-20210319153457812](/images/gulp.assets/image-20210319153457812.png)

## 命令

![image-20210319152641717](/images/gulp.assets/image-20210319152641717.png)

## Gulpfile

```js
/**
 * @Author: 柯军
 * @Date: 2019/10/16 15:13:41
 * @Description: 针对v51将一体化版本代码进行ES62ES5的转换
 */
const { gConsole, logger } = require('./gulp-module/utils');
// 引入相关工具方法
const { getParams, printInitInfo, updateVersion } = require('./gulp-module/function');
// 引入自定义babel方法
const TBabel = require('./gulp-module/task/TBabel');
// 引入Merge方法
const TMerge = require('./gulp-module/task/TMerge');
// 引入less处理方法
const TAllLess = require('./gulp-module/task/TAllLess');
// 引入文件打包方法
const { TAllMinJs, TCommonMinJs, TWebComponent } = require('./gulp-module/task/TAllMinJs');
// 引入目录清理方法
const TClean = require('./gulp-module/task/TClean');
const ts = require('gulp-typescript');
const gulp = require('gulp');

// 为了不让异常将线程结束，这里对异常进行捕获，不然开发环境下一个接口异常就会导致express服务终止~
process.on('uncaughtException', function(err) {
	logger.error(err);
	setTimeout(() => {
		console.error(err); // 抛出一个异步异常
		process.exit(1);
	}, 1000);
});

const { series } = require('gulp');
// 获取输入的命令参数
const watch = require('gulp-watch'),
 PARAMS = getParams(process.argv);
// express服务单独文件处理，这里引入即可
const appHttp = require('./gulp-module/app');
printInitInfo(PARAMS);

async function dev() {
  console.log()
  await handleDev();
}

async function dev_h6() {
  await handleDev('h6');
}

async function dev_pms() {
  await handleDev('pms');
}
// 开发环境公共处理方法
async function handleDev(type) {
  await updateVersion({ type: 'dev', data: { env: 'dev' } });
	await TAllLess(type);
  gConsole('初始化Less监听', 'blue');

  const fun = e => {
    const { history } = e,
      [url] = history;
    gConsole(`${url} => change`, 'yellow');
  }

  await Promise.all([TAllMinJs(), TAllLess(type), TCommonMinJs(), TWebComponent()]);
// 开发环境进行文件更改监听
  watch('./Res/modules/tradeTobeOne/src/**/*.less', e => {
    fun(e);
    TAllLess(type);
  });
  watch([
    './Res/modules/tradeTobeOne/src/sysconfig/webComponent/*.js',
  ], e => {
    fun(e);
    TWebComponent();
  });
  // 启动express服务
  appHttp();
}

async function build() {
  await handleBuild();
}

async function build_h6() {
  await handleBuild('h6');
}

async function build_pms() {
  await handleBuild('pms');
}

// build公共处理方法
async function handleBuild(type) {
  await TClean('./dist/Res/modules/tradeTobeOne/');
  await TBabel({
    sourceUrl: './Res/modules/tradeTobeOne/',
    targetUrl: './dist/Res/modules/tradeTobeOne/',
    config: PARAMS,
    type,
  });
  if (PARAMS.merge) {
    await TMerge(PARAMS);
  }
  console.log(`打包时间: ${new Date().toLocaleString()}`);
}

// 代码迁移方法
async function merge() {
  handleMerge('win')
}

// macos支持
async function merge_mac() {
  handleMerge('mac')
}

async function handleMerge(type) {
  console.log(type);
  TMerge(PARAMS);
}

async function allLess() {
  TAllLess();
}

// 处理TypeScript，暂时废弃，用不上，type问题解决起来工程量太大
async function babelTs() {
  await gulp.src([`./Res/**/*.ts`])
    .pipe(ts({
      noImplicitAny: true,
    }))
    .pipe(gulp.dest('./dist/Res'));
    // .pipe(gulp.dest(file => {
    //   console.log(file.base, file.history, file.path,file.cwd);
    //   const { base, path } = file;
    //   let url = path.replace(base, '').replace(/\\/g, '/');
    //   console.log(`./dist/Res${url}`)
    //   return `./dist/Res2${url}`
    // }));
}

exports.babelTs = series(babelTs);

exports.allLess = series(allLess);
exports.dev = series(dev);
exports.dev_h6 = series(dev_h6);
exports.dev_pms = series(dev_pms);

exports.build = series(build);
exports.build_h6 = series(build_h6);
exports.build_pms = series(build_pms);

exports.merge = series(merge);
exports.merge_mac = series(merge_mac);

```

## 开发环境

开发环境使用Express作为web服务，也可以使用nginx进行部署。

```js
const express = require('express');
const cors = require('cors');
const serveStatic = require('serve-static');
const { logger } = require('./utils/log4js');
const proxyLog = require("./utils/proxy_rewrite");
const proxyMiddleWare = require('http-proxy-middleware');
const config = require('./config/config');
const chalk = require('chalk');

module.exports = () => {
  const app = express();
  app.set('trust proxy', true);
  app.use(cors());
  let { http_port, proxy_option, static_page, apiUrl, tradeApiUrl } = config;

  app.use(
    '/',
    serveStatic(static_page.root, static_page.option),
  )
// Login.html文件单独处理
  app.use(
    '/Home/Login',
    serveStatic('./Home/Login.html'),
  )

    // 处理转发，使用的是http-proxy-middleware
  for (let x in proxy_option) {
    let option = proxy_option[x];
    option.logProvider = (provider) => {
      return proxyLog(provider, logger);
    };
    option.logLevel = 'debug';
    app.use(x, proxyMiddleWare(option));
  }

    // 捕获异常，防止向上传递导致服务终止
  try {
    const server = app.listen(http_port || 3000, function() {
      const port = server.address().port;
      console.log(chalk.blue(`\n      apiUrl: ${apiUrl} \n tradeApiUrl: ${tradeApiUrl} \n`));
      logger.error(`\n      apiUrl: ${apiUrl} \n tradeApiUrl: ${tradeApiUrl} \n`);
      // eslint-disable-next-line no-console
      console.log(chalk.blue(`${chalk.green('[✔]')} App listening at http://127.0.0.1:${port}`));
      logger.error(`[✔] App listening at http://127.0.0.1:${port}`);
    });
  } catch (e) {
    console.error(e);
  }
}

```

效果如下，会显示当前状态，会展示转发的目标站点以及当前web服务站点，使用不同版本命令会引入不同版本的样式文件和差异文件。例如

```shell
npm run dev
npm run dev:h6
npm run dev:pms
```

![image-20210319153753756](/images/gulp.assets/image-20210319153753756.png)

## Build

打包命令和开发命令一样区分版本，会根据版本不同打入对应的文件。

![image-20210319154303378](/images/gulp.assets/image-20210319154303378.png)

## less文件处理

```js
const gulp = require('gulp');
const gulpLess = require('gulp-less');
const concat = require('gulp-concat');
const gulpCleanCss = require('gulp-clean-css');
const through2 = require('through2');

module.exports = async function allLess(type, targetUrl) {
  return new Promise(success => {
    // 根据不同版本导入不同的样式文件    2020-09-03 15:03:31      --柯军
    let defaultCss = './Res/modules/tradeTobeOne/src/sysconfig/css/trade.less';
    if (type === 'h6') {
      defaultCss = './Res/modules/tradeTobeOne/src/sysconfig/css/trade_h6.less';
    } else if (type === 'pms') {
      defaultCss = './Res/modules/tradeTobeOne/src/sysconfig/css/trade_pms.less';
    }
    // 优先打包*.less文件，再打包*.module.less文件，所以*.less样式优先级较高    2020-09-03 15:02:28      --柯军
    gulp.src([
      defaultCss,
    ], { allowEmpty: true })
      .pipe(gulp.src([
        './Res/modules/tradeTobeOne/src/**/*.less',
        '!./Res/modules/tradeTobeOne/src/**/*.module.less',
        '!./Res/modules/tradeTobeOne/src/sysconfig/css/**/*.less',
      ]))
      .pipe(gulp.src(['./Res/modules/tradeTobeOne/src/**/*.module.less']))
      .pipe(through2.obj(function(file, _, cb) {
        // 处理*.module.less,限制样式只在该文件下生效，原理是获取*.module.less格式的文件，拿到目录，然后用目录作为一个classname，填充到样式文件的开头，这样他的样式就会限制在当前文件下有效
        if (file.isBuffer()) {
          const [url] = file.history;
          if (url.includes('.module.less')) {
            let sUrl = url.replace(/\\/g, '/').split('/Res/modules/')[1];
            sUrl = sUrl.replace(/\//g, '-').replace(/\.module\.less/, '');
            const className = `HY-${sUrl}`;
            const code = `#${className},.${className}{
              ${file.contents.toString()}
            }`;
            file.contents = Buffer.from(code);
          }
        }
        cb(null, file);
      }))
      .pipe(gulpLess())// 编译less
      .pipe(gulpCleanCss())
      .pipe(concat('site.min.css')) // 拼接成一个文件，并命名为style.css
      .pipe(gulp.dest(targetUrl || './Res/modules/tradeTobeOne/src/lib/').on('end', async () => {
          success();
        }));
  })
}

```

处理*.module.less,限制样式只在该文件下生效，原理是获取*.module.less格式的文件，拿到目录，然后用目录作为一个classname，填充到样式文件的开头，这样他的样式就会限制在当前文件下有效

![image-20210319155545752](/images/gulp.assets/image-20210319155545752.png)

## Babel

转换压缩后的代码会被写到指定的目录中

```js
/**
 * @createtime 2019/10/17 09:11:13
 * @author 柯军 <arthaskj@163.com>
 * @desc 统一处理gulp任务，方便添加压缩等其他步骤
 */
const { gConsole } = require('../utils');
const { updateVersion } = require('../function');
const allLess = require('./TAllLess');
const { TAllJsTask, TWebComponent } = require('./TAllMinJs');
const gulp = require('gulp');
const gutil = require('gulp-util');
const babel = require('gulp-babel');
const uglify = require('gulp-uglify');
const gulpIf = require('gulp-if');
const changed = require('gulp-changed');
const through2 = require('through2');
const fse = require('fs-extra');

function copyHome() {
  return new Promise((success, error) => {
    gConsole('开始迁移Home文件夹...');
    fse.copy('./Home/', './dist/Home/')
      .then(() => {
        gConsole('Home文件夹迁移成功！', 'green');
        success();
      })["catch"](err => {
      gConsole('Home文件夹迁移失败！', 'red');
      console.error(err);
      error();
    })
  })
}

function copyRes() {
  return new Promise((success, error) => {
    gConsole('开始迁移Res文件夹...');
    fse.copy('./Res/', './dist/Res/')
      .then(() => {
        gConsole('Res文件夹迁移成功！', 'green');
        success();
      })["catch"](err => {
      gConsole('Res文件夹迁移失败！', 'red');
      console.error(err);
      error();
    })
  })
}

module.exports = async function createGulpTask(option) {
  return new Promise(async success => {
    await copyHome();
    // await copyRes();
    let { sourceUrl, targetUrl, callback, type, config } = option,
      startTime = new Date().getTime();
    const { setVersion, createSourcemap } = config;
    gConsole(`Starting 'babel' 开始代码转换和迁移！`);
    // 不对lib文件夹进行操作，lib里的文件都是已经压缩后的源代码，转换时间长，也没必要转换    2020-03-09 16:31:49      --柯军
    let babelStream = await gulp.src([
      `${sourceUrl}**/*.*`,
      `!${sourceUrl}**/**.min.js`,
      `!${sourceUrl}src/lib/**/*.*`,
      `!${sourceUrl}src/**/api/**/*.*`,
      `!${sourceUrl}src/sysconfig/css/**/*.*`,
      `!${sourceUrl}src/sysconfig/component/**/*.*`,
      `!${sourceUrl}src/sysconfig/webComponent/**/*.*`,
      `!${sourceUrl}src/sysconfig/trade/**/*.*`,
      `!${sourceUrl}src/instruction_trader/public_method/*.js`,
	  `!${sourceUrl}src/**/_dev_config.js`,
      `!${sourceUrl}src/instruction_trader/children/cashSale/config_files/**/*.js`,
      `!${sourceUrl}src/instruction_trader/children/collateralisedRepo/config_files/**/*.js`,
      `!${sourceUrl}src/**/table_option_*.js`,
      `!${sourceUrl}src/**/*.less`,
      `!${sourceUrl}src/**/*.ts`,
    ], { sourcemaps: createSourcemap });

    babelStream = await babelStream.pipe(through2.obj(function(file, _, cb) {
		// 由于系统对接了很多外部系统，这里获取配置文件得版本信息并填写到全局变量
        if (file.isBuffer()) {
        const [url] = file.history;
        let sUrl = url.replace(/\\/g, '/');
        if (sUrl.includes('src/sysconfig/Trade_Branch')) {
          const code = `window.__trade_system_type__ = "${type}";` + file.contents.toString();
          file.contents = Buffer.from(code);
        }
      }
      cb(null, file);
    }))

      // babel转换
    babelStream = await babelStream.pipe(gulpIf('**/**.js', babel()))
      // 代码压缩
    babelStream = await babelStream.pipe(gulpIf('**/**.js', uglify().on('error', function (err) {
      gutil.log(gutil.colors.red(err)); // 错误定位
      this.end(); // 手动结束线程（可以不设置，默认自动结束）
    })))
    await babelStream.pipe(changed(targetUrl))
      .pipe(gulp.dest(targetUrl, { sourcemaps: !!createSourcemap }).on("end", async () => {
        let endTime = new Date().getTime(),
          time = endTime - startTime;
        gConsole(`Finished 'babel' 代码转换和迁移完成！ after ${time} ms!`, 'green');

        await allLess(type, `${targetUrl}src/lib/`);
        await TAllJsTask('./dist/Res/modules/tradeTobeOne/src/lib/');
        await TWebComponent('./dist/Res/modules/tradeTobeOne/src/lib/');
        // 更新版本信息
        await updateVersion({ type: 'build', data: { env: 'prod' }, setVersion, config, sysType: type });
        success();
        callback && callback();
      }))
  })
}

```

## 合并同类文件

合并同类文件可以优化页面加载速度。

```js
const gulp = require('gulp');
const gutil = require('gulp-util');
const uglify = require('gulp-uglify');
const babel = require('gulp-babel');
const concat = require('gulp-concat');
const gulpIf = require('gulp-if');
const through2 = require('through2');
const { gConsole } = require('../utils');

function TConcatJs(opt) {
  const { sourceUrl, fileName, processName, targetUrl, disBabel, disUglify, autoRun } = opt;
  return new Promise(async success => {
    let gulpStream = gulp.src(sourceUrl, { sourcemaps: true });
    if (!disBabel) {
      gulpStream = await gulpStream.pipe(gulpIf('**/**.js', babel()));
    }

    if (autoRun) {
      gulpStream = await gulpStream.pipe(through2.obj(function(file, _, cb) {
        if (file.isBuffer()) {
          const code = '(function(){' + file.contents.toString() + '})()';
          file.contents = Buffer.from(code);
        }
        cb(null, file);
      }))
    }

    if (!disUglify) {
      gulpStream = await gulpStream.pipe(gulpIf('**/**.js', uglify().on('error', function (err) {
        gutil.log(gutil.colors.red(err)); // 错误定位
        this.end(); // 手动结束线程（可以不设置，默认自动结束）
      })))
    }

    await gulpStream.pipe(concat(fileName))
      .pipe(gulp.dest(targetUrl || './Res/modules/tradeTobeOne/src/lib/', { sourcemaps: true }).on('end', async () => {
        gConsole(`初始化${processName}完成!`, 'green');
        success();
      }));
  })
}

// 组件文件以及api文件和一些公共方法直接打包到一个文件
function TCommonMinJs(targetUrl) {
  return TConcatJs({
    sourceUrl: [
      './Res/modules/tradeTobeOne/src/sysconfig/component/*.js',
      './Res/modules/tradeTobeOne/src/instruction_trader/api/request.js',
      './Res/modules/tradeTobeOne/src/**/api/modules/*.js',
      './Res/modules/tradeTobeOne/src/instruction_trader/api/index.js',
      './Res/modules/tradeTobeOne/src/sysconfig/trade/*.js',
      './Res/modules/tradeTobeOne/src/instruction_trader/public_method/*.js',
      './Res/modules/tradeTobeOne/src/instruction_trader/children/cashSale/config_files/**/*_HA.js',
      './Res/modules/tradeTobeOne/src/instruction_trader/children/collateralisedRepo/config_files/**/*_HA.js',
      './Res/modules/tradeTobeOne/src/instruction_trader/children/cashSale/config_files/**/*.js',
      './Res/modules/tradeTobeOne/src/instruction_trader/children/collateralisedRepo/config_files/**/*.js',
      './Res/modules/tradeTobeOne/src/**/table_option_*.js',
    ],
    fileName: 'common.min.js',
    processName: 'common.min.js',
    autoRun: true,
    targetUrl,
  });
}

async function TAllMinJs(targetUrl) {
  return new Promise(async success => {
    await gulp.src(['./Res/modules/tradeTobeOne/src/sysconfig/lib/before/*.min.js'], { sourcemaps: true })
      .pipe(gulp.src(['./Res/modules/tradeTobeOne/src/sysconfig/lib/later/*.min.js']))
      .pipe(gulpIf('**/box.common.min.js', babel()))
      .pipe(gulpIf('**/box.common.min.js', uglify().on('error', function (err) {
        gutil.log(gutil.colors.red(err)); // 错误定位
        this.end(); // 手动结束线程（可以不设置，默认自动结束）
      })))
      .pipe(concat('index.min.js'))
      .pipe(gulp.dest(targetUrl || './Res/modules/tradeTobeOne/src/lib/', { sourcemaps: true }).on('end', async () => {
        gConsole(`初始化index.min.js完成!`, 'green');
        success();
      }))
  })
}

function TAllJsTask(targetUrl) {
  return Promise.all([
    TAllMinJs(targetUrl),
    TCommonMinJs(targetUrl),
  ]);
  // return TCommonMinJs();
}

// 将webcomponent文件打包到单独文件
async function TWebComponent(targetUrl) {
  return TConcatJs({
    sourceUrl: [
      './Res/modules/tradeTobeOne/src/sysconfig/webComponent/*.js',
    ],
    fileName: 'web-component.min.js',
    processName: 'web-component.min.js',
    autoRun: true,
    targetUrl,
  });
}

module.exports = {
  TAllMinJs,
  TAllJsTask,
  TCommonMinJs,
  TWebComponent,
}

```

![image-20210319160319685](/images/gulp.assets/image-20210319160319685.png)

## Merge

由于这个项目对接了很多系统，而且功能在各个系统间都是通用的，所以一般一个新的功能会同步到很多版本上，如果不做处理，就需要先打包，在拷贝打包的文件到对应项目下，然后使用svn或git进行提交，四个项目就需要重复四次，这对于开发来说很麻烦，而且容易出错，特别是维护这个项目的开发只有我一个。

因此需要脚本来提升工作效率

```js
const { gConsole } = require('../utils');
const fse = require('fs-extra'),
 exec = require('child_process').exec;
const mergeUrl = require('../mergeUrl');

// 执行svn仓库命令~无限回调~
function runSvnCommand(targetUrl, msg) {
  return new Promise((success, error) => {
    exec(`svn add ${targetUrl}src/. --force `, err1 => {
      if (err1) {
        gConsole(`【SVN】提交失败! Failed => ${targetUrl} \n error:${err1}`);
        error(err1);
      } else {
        exec(`svn commit ${targetUrl}src/. -m "[Merge by Script]${msg || '默认代码更新'}"`, err2 => {
          if (err2) {
            gConsole(`【SVN】提交失败! Failed => ${targetUrl} \n error:${err2}`);
            error(err2);
          } else {
            gConsole(`【SVN】提交完成! OK => ${targetUrl}`);
            success();
          }
        });
      }
    });
  })
}

// 执行Git仓库命令
function runGitCommand(targetUrl, msg) {
  return new Promise((success, error) => {
    const commands = [
      `cd ${targetUrl}src/`,
      'git pull',
      'git add . -f',
      `git commit -m "[Merge by Script]${msg || '默认代码更新'}"`,
      'git push',
    ]
    exec(commands.join(' && '), err => {
      if (err) {
        gConsole(`【GIT】Git提交失败! Failed => ${targetUrl} \n error:${err}`);
        error();
      } else {
        gConsole(`【GIT】提交完成! OK => ${targetUrl}`);
        success();
      }
    });
  })
}

async function mergeFactory(targetUrl, type) {
  return new Promise((success, error) => {
    let sourceUrl = './dist/Res/modules/tradeTobeOne';
    fse.copy(sourceUrl, targetUrl)
      .then(() => {
        gConsole(`【MERGE】迁移完成! ${targetUrl} => success!`);
        if (process.argv.includes('--push')) {
          let msg = '';
          process.argv.forEach(x => {
            if (x.includes('--m=')) {
              msg = x.split('--m=')[1];
            }
          });
          if (type === 'svn') {
            runSvnCommand(targetUrl, msg);
          } else {
            runGitCommand(targetUrl, msg);
          }
        }
      })["catch"](err => {
        error();
        console.error(err)
      })
  })
}
module.exports = async (PARAMS) => {
  // const { mac, win } = mergeUrl;
  const { mergeUrlsType } = PARAMS;
  const urls = mergeUrl[mergeUrlsType || 'win'];
  await urls.forEach(async (item) => {
    const { url, type } = item;
    await mergeFactory(url.replace(/\\/g, '/') + '/', type || 'svn');
  });

}

```

配置文件，可以配置不同项目，这样公共的代码也可以提交，个性化的功能也可以提交，很灵活

![image-20210319160959717](/images/gulp.assets/image-20210319160959717.png)

执行命令和效果如下，svn，git都可以执行，终于可以解放了~

`npm run build -- --setV --merge=xbond --m=feat:测试功能添加 --push`

![image-20210319161812684](/images/gulp.assets/image-20210319161812684.png)

![image-20210319161824729](/images/gulp.assets/image-20210319161824729.png)

![image-20210319161847251](/images/gulp.assets/image-20210319161847251.png)

## 总结

这个项目四个后端就我一个前端，还要适配6、7个版本，不使用脚本来提高效率根本很难开发下去。其实就是压力变动力~