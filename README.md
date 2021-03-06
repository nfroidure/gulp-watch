# [gulp](https://github.com/gulpjs/gulp)-watch [![NPM version][npm-image]][npm-url] [![Build Status][travis-image]][travis-url] [![Dependency Status][depstat-image]][depstat-url]
> Watch, that you expect

This is mostly __proof-of-concept__ of streaming version for `gulp.watch`. API will change, maybe this plugin will be obsolete soon (likely move to `fs.watch` of `vinyl.fs`).

## Usage

### Trigger for mocha

```js
// npm i gulp gulp-watch gulp-mocha

var gulp = require('gulp');
var mocha = require('gulp-mocha');
var watch = require('gulp-watch');
var gutil = require('gulp-util')

gulp.task('mocha', function () {
    return gulp.src(['test/*.js'])
        .pipe(mocha({ reporter: 'list' }))
        .on('error', gutil.log);
});

gulp.task('watch', function() {
    gulp.src(['lib/**', 'test/**'], { read: false })
        .pipe(watch(function(events, cb) {
            gulp.run('mocha', cb);
        });
});

gulp.task('default', function () {
    gulp.run('mocha');
    gulp.run('watch');
});

// run `gulp watch` or just `gulp` for watching and rerunning tests

```


### Continious stream of events

```js
// npm i gulp gulp-watch gulp-sass

var gulp = require('gulp'),
    watch = require('gulp-watch'),
    sass = require('gulp-sass');

gulp.task('default', function () {
    gulp.src('scss/**', { read: false })
        .pipe(watch())
        .pipe(sass())
        .pipe(gulp.dest('./dist/'));
});
```

## API

### watch([options, callback])

This function creates have two different modes, that are depends on have you provice callback function, or not. If you do - you get __batched__ mode, if you not - you get __stream__.

__Callback signature__: `function(events, [done])`.

 * `events` - is `Array` of incoming events.
 * `done` - is callback for your function signal to batch, that you are done. This allows to run your callback as soon as previous end.

__Options__:

This object passed to [`gaze` options](https://github.com/shama/gaze#properties) directly, so see documentation there.

For __batched__ mode we are using [`gulp-batch`](https://github.com/floatdrop/gulp-batch#api), so options from there are available.

__Methods__:

Returned Stream from constructor have some useful methods:

 * `close()` - calling `gaze.close` and emitting `end`, after `gaze.close` is done.

__Events__:

 * `end` - all files are stop being watched.
 * `ready` - all files, that are passed from `gulp.src`, are now being watched.
 * `error` - when something happened inside callback, you will get notified.

__Returns__:

 * __Batched mode__  - wrapped callback, that will gather events and call callback.
 * __Stream mode__ - stream, that handles `gulp.src` piping.

# License

(MIT License)

Copyright (c) 2013 Vsevolod Strukchinsky (floatdrop@gmail.com)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

[npm-url]: https://npmjs.org/package/gulp-watch
[npm-image]: https://badge.fury.io/js/gulp-watch.png

[travis-url]: http://travis-ci.org/floatdrop/gulp-watch
[travis-image]: https://travis-ci.org/floatdrop/gulp-watch.png?branch=master

[depstat-url]: https://david-dm.org/floatdrop/gulp-watch
[depstat-image]: https://david-dm.org/floatdrop/gulp-watch.png
