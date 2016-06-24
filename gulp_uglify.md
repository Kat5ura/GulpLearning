# Gulp 插件 —— gulp-uglify

> 使用 **UglifyJs** 压缩js文件。

## 用法
```javascript
var gulp = require('gulp');
var uglify = require('gulp-uglify');
var pump = require('pump');
 
gulp.task('compress', function (cb) {
  pump([
        gulp.src('lib/*.js'),
        uglify(),
        gulp.dest('dist')
    ],
    cb
  );
});

```

为了合适的处理Node流的错误，推荐使用 [pump](https://github.com/mafintosh/pump),点击[这里](https://github.com/terinjokes/gulp-uglify/blob/master/docs/why-use-pump/README.md#why-use-pump)，了解为何使用它。

## Options

 - mangle
传 **false** 来跳过混淆 ***name（变量名）***

 - output
要指定额外的输出配置，传一个对象给他。默认的 ***options*** 值已经最优。

 - compress
传递一个对象来自定义压缩。设置为 ***false*** 将完全跳过压缩。

 - preserveComments
    ***options.output.comments***的快捷选项，默认不保留注释。  

    - all
    保留全部的注释。
    
    - license
    会保留可能的关于协议的信息，不管是不是包含@license 或者 /*!这样的指令。
    这个配置通过 ***uglify-save-license***模块来实现的，只要满足下面情况中的任意一个，则被保留。
        - 注释为文件的第一行。
        - 注释中的内容正则匹配到特定的字符串，例如MIT, @license, 或者 Copyright
        - There is a comment at the previous line, and it matches 1, 2, or 3.
    - function
    指定你自己的预留注释的逻辑，你将在方法中操作当前的节点以及当前的注释，你需要返回 true 或者 false。

也可以传一个 uglify 方法，来更改 UglifyJs 的默认行为，所有的选项点击[这里](https://github.com/mishoo/UglifyJS2#the-simple-way)。

## Errors

当无法压缩某个文件时，***gulp-uglify*** 会触发一个 ***error***事件。***PluginError***对象中可能包含以下几个字段，
 
 - fileName
 - lineNumber
 - message

## 使用不同版本的UglifyJS
默认的使用安装时依赖的版本，你可以通过 ***minifier***接口来进行配置使用不同版本的

```javascript
var uglifyjs = require('uglify-js'); // can be a git checkout 
                                     // or another module (such as `uglify-js-harmony` for ES6 support) 
var minifer = require('gulp-uglify/minifier');
var pump = require('pump');
 
gulp.task('compress', function (cb) {
  // the same options as described above 
  var options = {
    preserveComments: 'license'
  };
 
  pump([
      gulp.src('lib/*.js'),
      minifier(options, uglifyjs),
      gulp.dest('dist')
    ],
    cb
  );
});
```

