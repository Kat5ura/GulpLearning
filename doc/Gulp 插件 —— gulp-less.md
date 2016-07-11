# Gulp插件 —— gulp-less 

标签（空格分隔）： Gulp Plugin

> gulp 下的less插件。

## 用法

```javascript
var less = require('gulp-less');
var path = require('path');
 
gulp.task('less', function () {
  return gulp.src('./less/**/*.less')
    .pipe(less({
      paths: [ path.join(__dirname, 'less', 'includes') ]
    }))
    .pipe(gulp.dest('./public/css'));
});
```

## Options

可用的 ***options*** 可以在[less官方文档](http://lesscss.org/#using-less-configuration) 中找到。下面列出了编码是合法的配置：

 - *path*： ***@import***指令使用到的路劲的数组。
 - *plugins*： less 插件的数组。

*filename*： 选项不是必须的，因为gulp-less会帮你处理好。
*compress*： 选项是不被支持的，如果你希望压缩你的css，你需要使用一个css压缩插件。
*sourceMap*：没有这个选项，如果你要生成sourcemaps，请使用[gulp-sourcemaps](https://github.com/floridoo/gulp-sourcemaps)

## 使用插件

Less 现在支持很多插件，他们可以为你提供很多额外的特性。下面是一个配合插件使用的例子：

```javascript
var LessAutoprefix = require('less-plugin-autoprefix');
var autoprefix = new LessAutoprefix({ browsers: ['last 2 versions'] });
 
return gulp.src('./less/**/*.less')
  .pipe(less({
    plugins: [autoprefix]
  }))
  .pipe(gulp.dest('./public/css'));
```

你可以在 http://lesscss.org/usage/#plugins 找到更多关于Less插件的信息，这里囊括了现阶段所有的插件列表。

## Source Maps

gulp-less 可以与 [gulp-sourcemaps](https://github.com/floridoo/gulp-sourcemaps) 配合使用来为你的文件生成 source map。你需要在编译 less 代码之前初始化 gulp-sourcemaps，之后写入文件，就像这样：

```javascript
var sourcemaps = require('gulp-sourcemaps');
 
gulp.src('./less/**/*.less')
  .pipe(sourcemaps.init())
  .pipe(less())
  .pipe(sourcemaps.write())
  .pipe(gulp.dest('./public/css'));
```
默认的 source map 会被写入压缩了的css中的一行，要分开写，只需为 sourcemaps.write() 方法指定一个相对路径即可。

```javascript
var sourcemaps = require('gulp-sourcemaps');
 
gulp.src('./less/**/*.less')
  .pipe(sourcemaps.init())
  .pipe(less())
  .pipe(sourcemaps.write('./maps'))
  .pipe(gulp.dest('./public/css'));
  
```

## 错误处理

在 gulpjs 4.0 发布之前, 你可以使用 ***[gulp-plumber](https://www.npmjs.com/package/gulp-plumber)*** 来防止在错误时中止进程。

