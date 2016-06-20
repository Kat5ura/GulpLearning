# 什么是 gulp

## 前端工程化

### 工程化

> 工程化即系统化、模块化、规范化的一个过程。指将具有一定规模数量的单个系统或功能部件，按照一定的规范，组合成一个模块鲜明、系统性强的整体。

### 工程化要点

- 制定标准 / 规范
- 提升效率

### 前端的问题

- 重复工作；如通用的流程控制机制，可扩展的UI组件、灵活的工具方法
- 重复优化；如降低框架层面升级带给业务团队的耗损、帮助业务在无感知情况下做掉大部分优化（比如打包压缩什么的）
- 开发效率；如帮助业务团队写可维护的代码、让业务团队方便的调试代码（比如Hybrid调试）

> 以上问题均属于工程化的问题, 都可以也需要通过工程化的方法来解决

### 将前端引向工程化

前端工程大概包括以下几点
>引用知乎[Kevin](http://www.zhihu.com/question/41894686/answer/92966499)的回答

> - 模块化与组件化: npm, es6, react/angularjs, RequireJs, seaJs...
> - 代码版本管理: git/svn
> - 代码风格管理: jscs, editorconfig
> - 代码编译: Babel/CoffeeScript/TypeScript, scss, imgmin, csssprit, inline-svg
> - 代码质量管理 (QA): eslint, mocha/karma
> - 代码构建: webpack/gulp/grunt
> - 项目脚手架: yeoman
> - 持续集成/持续交付/持续部署: jenkins
> - 本地化与国际化


>  几个人准备原料,煲玉米排骨汤，如果不进行工程管理的话，把每个人的原料拿过来放在锅里煮，当然弄出来也能吃，但这个好吃的概率就很渺茫了。用工程化的思路来管理：
>  +  准备好菜谱,分好工
>  +  准备工作 --- 玉米, 排骨, 调味料, 厨具... 按照菜谱处理好...
>  +  开干之前，大家得统一一下意见，餐具怎么摆，刀用完了是挂起来还是插到刀架上？所有的刀具是从大到小摆放还是从小到大摆放？要做到随便谁用完了厨房，下一个人进来的时候，都能熟悉所有的刀具的位置。
>  +  做的时候，可能各种炊具的最有效率的方式不一样，砂锅,和高压锅又不一样
>  +  人一多，原料一多，事件就复杂了，有时候为了避免麻烦，还是把所有人做的每一件事都记录下来比较好，万一发现问题，也有好找记录嘛。这就是版本管理。
>  +  煲的过程中,咱们得有个仪器，监测过程中的各项指标,温度,浓度,咸度...任何人在里面加了什么东西以后，这个仪器都会查下，看会不会影响最终的成果
>  +  最后汤煲好了,出锅之后再来个摆盘，雕个萝卜啊, 撒点花瓣啊啥的。
>  +  很多其实很多时候不但每种原料有人准备， 整个菜的原料搭配说不定都有人给你做好了，超市买个那种洗好切好的方便菜回来，自己在这个基础上直接下锅,就省了很多烦心事
>  +  后来我嫌麻烦了,搞了一个机器人,我发一个指令,机器人自动帮我出一碗玉米排骨汤.`````````````````_````````````````````````````````````````````````````````````````````_`````````````````

### 前端工程化中的 Gulp
> 代码构建: webpack/gulp/grunt

## 走近 Gulp

> * Gulp.js 是一个自动化构建工具，开发者可以使用它在项目开发过程中自动执行常见任务。
> * Gulp.js 是基于 Node.js 构建的，利用 Node.js 流的威力，你可以快速构建项目并减少频繁的 IO 操作。(相对于Grunt)
> * Gulp.js 源文件和你用来定义任务的 Gulp 文件都是通过 JavaScript（或者 CoffeeScript ）源码来实现的。

### 基于流的

类似于linux中的管道命令' | '
```shell
// 过滤历史终端命令中带有'cp'的条目,打印到终端
history | grep 'cp'
```

以 gulp-sass 插件为例:

```javascript
'use strict';

// 引入gulp和gulp-sass模块
var gulp = require('gulp');
var sass = require('gulp-sass');

// 创建sass任务
gulp.task('sass', function () {
  return gulp.src('./sass/**/*.scss') //获取sass文件
    .pipe(sass().on('error', sass.logError)) // 调用gulp-sass进行编译
    .pipe(gulp.dest('./css')); // 将生成文件放入目标目录
});

// 创建sass文件的监听任务
gulp.task('sass:watch', function () {
  // 当sass文件夹下文件发生变化后,自动执行sass任务,重新进行编译
  gulp.watch('./sass/**/*.scss', ['sass']);
});

```

关注此处的 **pipe** 方法
- 将src方法读取的文件内容写入流中
- pipe方法将流直接传给 **gulp-sass** 插件进行处理,并返回流结果
- pipe方法将流传递给dest方法,生成目标文件


### 安装 Gulp

``` shell
// 全局安装 Gulp
sudo npm install -g gulp

// 作为项目依赖安装 Gulp
npm install gulp --save
```

### gulpfile.js

gulp的主文件,gulp的任务全部定义于此

```JavaScript
// 引入gulp和gulp-sass模块
var gulp = require('gulp');
var sass = require('gulp-sass');

// 创建sass任务
gulp.task('sass', function () {
  return gulp.src('./sass/**/*.scss') //获取sass文件
    .pipe(sass().on('error', sass.logError)) // 调用gulp-sass进行编译
    .pipe(gulp.dest('./css')); // 将生成文件放入目标目录
});

// 创建sass文件的监听任务
gulp.task('sass:watch', function () {
  // 当sass文件夹下文件发生变化后,自动执行sass任务,重新进行编译
  gulp.watch('./sass/**/*.scss', ['sass']);
});

gulp.task('default', function() {
  // 将你的默认的任务代码放在这
});
```

```shell
// 执行 gulp 任务
// 默认执行default任务
gulp

// 执行指定任务
gulp sass

```

### 什么是 Gulp

> 用自动化构建工具增强你的工作流程！

* 自动化构建工具
* 增强工作流程
