# Gulp 插件 —— gulp-connect

> 可以在本地跑一个 **webserver**，自带 **LiveReload**。

## 用法

```javascript
var gulp = require('gulp'),
  connect = require('gulp-connect');
 
gulp.task('connect', function() {
  connect.server();
});
 
gulp.task('default', ['connect']);
```

### 添加 *livereload*

```javascript
var gulp = require('gulp'),
  connect = require('gulp-connect');
 
gulp.task('connect', function() {
  connect.server({
    root: 'app',
    livereload: true
  });
});
 
gulp.task('html', function () {
  gulp.src('./app/*.html')
    .pipe(connect.reload());
});
 
gulp.task('watch', function () {
  gulp.watch(['./app/*.html'], ['html']);
});
 
gulp.task('default', ['connect', 'watch']);
```

### 启动/停止 *server*

```javascript
gulp.task('jenkins-tests', function() {
  connect.server({
    port: 8888
  });
  // run some headless tests with phantomjs 
  // when process exits: 
  connect.serverClose();
});
```

### 配置多个 *server* 

```javascript
var gulp = require('gulp'),
  stylus = require('gulp-stylus'),
  connect = require('gulp-connect');
 
gulp.task('connectDev', function () {
  connect.server({
    root: ['app', 'tmp'],
    port: 8000,
    livereload: true
  });
});
 
gulp.task('connectDist', function () {
  connect.server({
    root: 'dist',
    port: 8001,
    livereload: true
  });
});
 
gulp.task('html', function () {
  gulp.src('./app/*.html')
    .pipe(connect.reload());
});
 
gulp.task('stylus', function () {
  gulp.src('./app/stylus/*.styl')
    .pipe(stylus())
    .pipe(gulp.dest('./app/css'))
    .pipe(connect.reload());
});
 
gulp.task('watch', function () {
  gulp.watch(['./app/*.html'], ['html']);
  gulp.watch(['./app/stylus/*.styl'], ['stylus']);
});
 
gulp.task('default', ['connectDist', 'connectDev', 'watch']);
```

## API

 - options.root
Type: Array or String
Default: ***gulpfile*** 所在目录
一般为项目根目录

 - options.port
Type: Number
Default: 8080
***webserver** 监听的端口

 - options.host
Type: String
Default: localhost

 - options.https
Type: Object
Default: false
可以是[本文档](https://nodejs.org/api/https.html#https_https_createserver_options_requestlistener)中的任何选项。
当 ***https*** 设置为 **true** 时，一些内置的默认选项将被使用。

 - options.livereload
Type: Object or Boolean 
Default: false

    - options.livereload.port
    Type: Number
    Default: 35729
    
    - options.livereload.hostname
    Type: String
    Default: 'undefined'

    重写 ***livereload*** 注入到 *index.html* 中的 *hostname*

 - options.fallback
Type: String
Default: undefined
回退文件 (e.g. index.html)

 - options.middleware
Type: Function
Default: []

 - options.debug
Type: Boolean
Default: false

```javascript
gulp.task('connect', function() {
  connect.server({
    root: "app",
    middleware: function(connect, opt) {
      return [
        // ... 
      ]
    }
  });
});
```

