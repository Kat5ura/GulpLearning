# Gulp 插件 —— gulp-filter

> 利用模式匹配过来过滤一批原始文件，我们可以对匹配成功的这个子集里的文件进行操作。当对子集处理完之后，如果你希望对完整的文件集合做操作，你只需要**恢复（restore）**流。

## 用法

### 只过滤

如果你只需要过滤出你想要的文件并处理它：

```javascript
const gulp = require('gulp');
const uglify = require('gulp-uglify');
const filter = require('gulp-filter');

gulp.task('default', () => {
    // create filter instance inside task function
    const f = filter(['*', '!src/vendor']);

    return gulp.src('src/*.js')
        // filter a subset of the files
        .pipe(f)
        // run them through a plugin
        .pipe(uglify())
        .pipe(gulp.dest('dist'));
});
```

### 使用了恢复流的方法

```javascript
const gulp = require('gulp');
const uglify = require('gulp-uglify');
const filter = require('gulp-filter');

gulp.task('default', () => {
    // 在任务的方法里，实例化一个 ***filter**
    const f = filter(['*', '!src/vendor'], {restore: true});

    return gulp.src('src/*.js')
        // 过滤得到要处理的文件集合的子集
        .pipe(f)
        // 在插件中处理这个子集
        .pipe(uglify())
        // 将之前过滤剔除的文件集合重新与当前过滤得出的子集合并（可选）。
        .pipe(f.restore)
        .pipe(gulp.dest('dist'));
});
```

### 使用多个过滤器

通过*合并*、*恢复*不同的过滤器，我们可以在同一个管道中对不同的文件子集进行操作。

```javascript
const gulp = require('gulp');
const less = require('gulp-less');
const concat = require('gulp-concat');
const filter = require('gulp-filter');

gulp.task('default', () => {
    const jsFilter = filter('**/*.js', {restore: true});
    const lessFilter = filter('**/*.less', {restore: true});

    return gulp.src('assets/**')
        .pipe(jsFilter)
        .pipe(concat('bundle.js'))
        .pipe(jsFilter.restore)
        .pipe(lessFilter)
        .pipe(less())
        .pipe(lessFilter.restore)
        .pipe(gulp.dest('out/'));
});
```

### 恢复为文件源

我们还可以在另外的地方去**恢复**文件，然后把它当做一个可读的流来用。只需将 ***passthrough*** 设置为 ***false*** 即可。

```javascript
const gulp = require('gulp');
const uglify = require('gulp-uglify');
const filter = require('gulp-filter');

gulp.task('default', () => {
    const f = filter(['*', '!src/vendor'], {restore: true, passthrough: false});

    const stream = gulp.src('src/*.js')
        // 过滤得到一个文件子集
        .pipe(f)
        // 插件中处理之
        .pipe(uglify())
        .pipe(gulp.dest('dist'));

    // 将过滤后的文件当做一个 gulp 文件源来使用。
    f.restore.pipe(gulp.dest('vendor-dist'));

    return stream;
});
```

## API

### filter(pattern, [options])
返回一个转换过的流，它拥有一个 ***restore*** 方法。

- **pattern**
类型: string, array, function
接受一个代表模式匹配的字符串或者数组，他们会在运行时使用多重匹配。
Accepts a string/array with globbing patterns which are run through multimatch.
如果你传递一个方法，会通过第一个参数得到一个 ***vinyl*** 文件对象，你需要传递 ***true*** 或者 ***false*** 来决定是否要包含这个文件。
If you supply a function you'll get a vinyl file object as the first argument and you're expected to return true/false whether to include the file:
```javascript
filter(file => /unicorns/.test(file.path));
```
- options
类型: object
接受 [minimatch options](https://github.com/isaacs/minimatch#options).
> 如果你需要匹配以 ***.*** 开头的文件 (eg. .gitignore)，请设置 ***dot: true***.

    - options.restore
类型: boolean
默认值: false
恢复过滤掉的文件。

    - options.passthrough
类型: boolean
默认值: true
设置为 ***true*** 时，过滤掉的文件会通过一个 ***PassThrough*** 流来恢复，设置为 ***false*** 时，过滤掉的文件会恢复为一个 ***可读（Readable）*** 流。
当流为***可读（Readable）***时，他会自行结束，但是当流为 ***PassThrough*** 流时，你需要处理它，来结束流。

