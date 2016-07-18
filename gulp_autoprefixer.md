# Gulp 插件 —— gulp-autoprefixer

> 使用 [autoprefixer](https://github.com/postcss/autoprefixer) 为 css 添加厂商前缀。

## 用法

```javascript
var gulp = require('gulp');
var autoprefixer = require('gulp-autoprefixer');

gulp.task('default', function () {
    return gulp.src('src/app.css')
        .pipe(autoprefixer({
            browsers: ['last 2 versions'],
            cascade: false
        }))
        .pipe(gulp.dest('dist'));
});
```
### API

#### autoprefixer([options])
***options*** 请参照 ***autoprefixer*** 的 [options](https://github.com/postcss/autoprefixer#options).


### Source Maps

像下面这样，配合 ***gulp-sourcemaps*** 使用：

```javascript
var gulp = require('gulp');
var sourcemaps = require('gulp-sourcemaps');
var autoprefixer = require('gulp-autoprefixer');
var concat = require('gulp-concat');

gulp.task('default', function () {
    return gulp.src('src/**/*.css')
        .pipe(sourcemaps.init())
        .pipe(autoprefixer())
        .pipe(concat('all.css'))
        .pipe(sourcemaps.write('.'))
        .pipe(gulp.dest('dist'));
});
```


