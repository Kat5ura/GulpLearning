# Gulp 插件 —— gulp-rev

> 在静态资源文件名的结尾添加文件 *hash* 作为其版本号。例如：***common.css*** => ***common-d41d8cd98f.css***
Make sure to set the files to never expire for this to have an effect.
确保设置文件为永不过期的，这将有意想不到的效果（呵呵。。。。）。

## 用法

```javascript
var gulp = require('gulp');
var rev = require('gulp-rev');

gulp.task('default', function () {
    return gulp.src('src/*.css')
        .pipe(rev())
        .pipe(gulp.dest('dist'));
});
```

## API

### rev()

### rev.manifest([path], [options])

- path
类型：string
默认值：*'rev-manifest.json'*
***manifest*** 文件路径。

- options
    - base
类型: string
默认值: process.cwd()
重写 *manifest* 文件的基础路径。

    - cwd
类型: string
默认值: process.cwd()
重写 *manifest* 文件所在的当前工作目录

    - merge
类型: boolean
默认值: false
与已存在的 *manifest* 文件进行合并。

    - transformer
类型: object
默认值: JSON
一个带有 **parse** 和 **stringify** 方法的对象。这个可以用来提供一个自定义的转换器来替换默认的 **JSON** 对象。

### Original path
***file.revOrigPath*** 存储的原始文件路径。当你在做重写静态资源的引用之类的事情时，可以手动去获取。

### Asset hash
每个版本化的文件的 **hash** 被存储在 ***file.revHash***，你可以使用它来自定义文件名，或者构建不同的 *manifest* 格式。

### Asset manifest
```javascript
var gulp = require('gulp');
var rev = require('gulp-rev');

gulp.task('default', function () {
    // 默认的, gulp 会选取 `assets/css` 作为 base 路径,
    // 所以我们需要明确的去设置它
    return gulp.src(['assets/css/*.css', 'assets/js/*.js'], {base: 'assets'})
        // 将原始文件复制到构建目录
        .pipe(gulp.dest('build/assets'))
        .pipe(rev())
        // 将版本化后的文件写入构建目录
        .pipe(gulp.dest('build/assets'))
        .pipe(rev.manifest())
        // 将 manifest 文件写入构建目录
        .pipe(gulp.dest('build/assets'));
});
```
存储了文件原始路径与版本化之后路径的对应关系的 *manifest* 文件，会被写入 *build/assets/rev-manifest.json*。
```json
{
    "css/unicorn.css": "css/unicorn-d41d8cd98f.css",
    "js/unicorn.js": "js/unicorn-273c2cin3f.js"
}
```
默认的，*rev-manifest.json* 会被完全替换。要与当前已存在的 *manifest*，想 ***rev.manifest()*** 方法传递 ***merge:true*** 和目标路径（通过 *base*）。
```javascript
var gulp = require('gulp');
var rev = require('gulp-rev');

gulp.task('default', function () {
    // by default, gulp would pick `assets/css` as the base,
    // so we need to set it explicitly:
    return gulp.src(['assets/css/*.css', 'assets/js/*.js'], {base: 'assets'})
        .pipe(gulp.dest('build/assets'))
        .pipe(rev())
        .pipe(gulp.dest('build/assets'))
        .pipe(rev.manifest({
            base: 'build/assets',
            merge: true // merge with the existing manifest (if one exists)
        }))
        .pipe(gulp.dest('build/assets'));
});
```
你也可以直接调用 ***rev.manifest('manifest.json')*** 方法来指定一个不同的路径或者文件名。

### Sourcemaps 与 gulp-concat
由于 **gulp-concat** 会处理文件路径，你可能需要在你的 **gulp-concat** 实例设置好 *cwd* 和 *path*，才能跑的通。
```javascript
var gulp = require('gulp');
var rev = require('gulp-rev');
var sourcemaps = require('gulp-sourcemaps');
var concat = require('gulp-concat');

gulp.task('default', function () {
    return gulp.src('src/*.js')
        .pipe(sourcemaps.init())
        .pipe(concat({path: 'bundle.js', cwd: ''}))
        .pipe(rev())
        .pipe(sourcemaps.write('.'))
        .pipe(gulp.dest('dist'));
```

### Streaming

本插件并不支持 **streaming**。如果你有一个来自 **streaming** 源的文件，例如 **browserify**，在管道中，你需要在 **gulp-rev** 之前使用 **gulp-buffer** 来处理一下。

```javascript
var gulp = require('gulp');
var browserify = require('browserify');
var source = require('vinyl-source-stream');
var buffer = require('gulp-buffer');
var rev = require('gulp-rev');

gulp.task('default', function () {
    return browserify('src/index.js')
        .bundle({debug: true})
        .pipe(source('index.min.js'))
        .pipe(buffer())
        .pipe(rev())
        .pipe(gulp.dest('dist'))
});
```


### Integration
为了更好的将 **gulp-rev** 整合进你的 App,可以查阅[官方指南](https://github.com/sindresorhus/gulp-rev/blob/master/integration.md)。

与 **gulp-rev** 配合使用：

- gulp-rev-replace - 重写所有被重命名的文件的引用
- gulp-rev-css-url - 重写CSS中被版本化后的资源的URL
- gulp-rev-outdated - Old static asset revision files filter
gulp-rev-collector - 静态资源版本数据控制器
rev-del - 删除老旧的不在被使用的资源
- gulp-rev-delete-original - 版本化之后删除原始文件
- gulp-rev-loader - 在 **webpack** 下使用 *rev-manifest*。
- gulp-rev-format - 提供了为静态文件的 *hash* 格式化的选项(前缀:prefix, 后缀:suffix, 扩展名:last-extension)




