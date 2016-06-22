# Gulp 插件 —— gulp-util

**[gulp-util](https://github.com/gulpjs/gulp-util)** **为 Gulp 插件提供的一组工具方法**

## Util Functions
包括了一下方法：

 - log(msg...)
 - colors
 - replaceExtension(path, newExtension)
 - beep()
 - isStream(obj)
 - isBuffer(obj)
 - template(string[, data])
 - new File(obj)
 - noop()
 - buffer(cb)
 - new PluginError(pluginName, message[, options])

### log(msg...)

 - 用于打日志，它已经自动为你添加了[gulp]前缀以及所有你希望日志应该具备的一切特性。
 - 如果你传入了多个参数，它会把他们拼接在一起并用空格分隔开。
 - 使用 colors 作为默认的配色方案

> - values (files, module names, etc.) = cyan
> - numbers (times, counts, etc) = magenta
 
```javascript
gutil.log('stuff happened', 'Really it did', gutil.colors.magenta('123'));
```

### colors

**[chalk](https://github.com/chalk/chalk)** **的实例**

> **chalk** 是终端字符样式美化工具库

### replaceExtension(path, newExtension)
 - 替换文件的扩展名。
 
```javascript
gutil.replaceExtension('file.coffee', '.js'); // file.js 
```

### isStream(obj)
 - 判断一个对象是否为**Stream**
 
### isBuffer(obj)
 - 判断一个对象是否为**Buffer**
 
### template(string[, data])
 - 这是一个对 ***lodash.template*** 方法的封装，你必须传入一个合法的 Gulp File 对象，用户才能使用它，否则将返回一个错误。注意你无法更改标识符。你可以查看 **[lodash](https://lodash.com/docs#template)** 来了解更多信息。

### new File(obj)

 - 这是一个 **[vinyl](https://github.com/gulpjs/vinyl)**
 
```javascript
var file = new gutil.File({
  base: path.join(__dirname, './fixtures/'),
  cwd: __dirname,
  path: path.join(__dirname, './fixtures/test.coffee')
});
```

### noop()
 - 返回一个可以直接传入数据，不做其他任何操作的**stream**
 
```javascript
// gulp should be called like this :
// $ gulp --type production
gulp.task('scripts', function() {
  gulp.src('src/**/*.js')
    .pipe(concat('script.js'))
    .pipe(gutil.env.type === 'production' ? uglify() : gutil.noop())
    .pipe(gulp.dest('dist/'));
});
```

### buffer(cb)

 - 与 ***es.wait*** 类似，区别是后者将字符buffer进一个字符串， 而前者将一切buffer进一个数组，这点对于file对象来说非常有用。
 - 返回一个可以pipe进数据的Stream
 - 当写入结束时，Stream 会发射一个 data 事件，这个data与传入回调中的数组是同一个。
 - 回调是可选的，它接收两个参数 ***err*** 和 ***data***。
 
```javascript
gulp.src('stuff/*.js')
  .pipe(gutil.buffer(function(err, files) {

  }));
```

### new PluginError(pluginName, message[, options])

 - ***pluginName*** 是你的插件的 module 名
 - ***message*** 可以是一个 string 或者一个存在的 error
 - 默认不会显示调用 stack。如果你需要查看调用栈信息来帮助差错，将**options.** ***showStack*** 为 true。
 - 如果你向 ***message*** 传入一个 ***error***，调用栈将通过 message 来展示，否则将创建一个新的。
 - 注意： 如果你传入了一个自定义的栈名，你需要同时传入一个 ***message***。
 - **Error** 的属性将显示在***err.toString()***的结果中。 可以通过设置options中的 *{showProperties: false}* 来关闭它。

下面的几种实例化形式都是ok的：
```javascript
var err = new gutil.PluginError('test', {
  message: 'something broke'
});

var err = new gutil.PluginError({
  plugin: 'test',
  message: 'something broke'
});

var err = new gutil.PluginError('test', 'something broke');

var err = new gutil.PluginError('test', 'something broke', {showStack: true});

var existingError = new Error('OMG');
var err = new gutil.PluginError('test', existingError, {showStack: true});
```

