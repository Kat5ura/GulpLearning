# Gulp 插件 —— gulp-concat

> 合并文件

## 基本用法
```javascript
var concat = require('gulp-concat');
gulp.task('scripts', function() {
  return gulp.src('./lib/*.js')
    .pipe(concat('all.js'))
    .pipe(gulp.dest('./dist/'));
});
```

它会使用操作系统的换行符来合并文件，并且使用传入的第一个文件的根目录来作为根目录。

文件会根据 gulp-src 方法中指定的文件顺序进行合并。例如要按照这个顺序合并./lib/file3.js, ./lib/file1.js 和 ./lib/file2.js这3个文件，可以像下面这样创建此任务：

```javascript
var concat = require('gulp-concat');
 
gulp.task('scripts', function() {
  return gulp.src(['./lib/file3.js', './lib/file1.js', './lib/file2.js'])
    .pipe(concat('all.js'))
    .pipe(gulp.dest('./dist/'));
});
```
要更改默认的换行符，只需要传入第二个参数即可，它可以是任意的字符。（如果要支持所有OS请设置为\r\n）

例如：
```javascript
.pipe(concat('main.js', {newLine: ';'}))
```

要指定cwd, path 和其他的 [vinyl](https://github.com/wearefractal/vinyl) 属性，为第一个参数传入一个对象。

```javascript
var concat = require('gulp-concat');
gulp.task('scripts', function() {
  return gulp.src(['./lib/file3.js', './lib/file1.js', './lib/file2.js'])
    .pipe(concat({ path: 'new.js', stat: { mode: 0666 }}))
    .pipe(gulp.dest('./dist'));
});
```
这将会把文件合并到 ***./dist/new.js***

## Source maps
Source maps可以使用 [gulp-sourcemaps](https://www.npmjs.org/package/gulp-sourcemaps) 来生成。

```javascript
var gulp = require('gulp');
var concat = require('gulp-concat');
var sourcemaps = require('gulp-sourcemaps');
 
gulp.task('javascript', function() {
  return gulp.src('src/**/*.js')
    .pipe(sourcemaps.init())
      .pipe(concat('all.js'))
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('dist'));
});
```