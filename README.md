# postcss-px-to-viewport [![NPM version](https://badge.fury.io/js/postcss-px-to-viewport.svg)](http://badge.fury.io/js/postcss-px-to-viewport)

- A plugin for [PostCSS](https://github.com/ai/postcss) that generates viewport units (vw, vh, vmin, vmax) from pixel units.
- fork from [evrone/postcss-px-to-viewport](https://github.com/evrone/postcss-px-to-viewport)

## Install

```bash
$ npm i @lyl/postcss-px-to-viewport -D
```

## Usage

If your project involves a fixed width, this script will help to convert pixels into viewport units.

### Input/Output

```css
// input

.class {
  margin: -10px 0.5vh;
  padding: 5vmin 9.5px 1px;
  border: 3px solid black;
  border-bottom-width: 1px;
  font-size: 14px;
  line-height: 20px;
}

.class2 {
  border: 1px solid black;
  margin-bottom: 1px;
  font-size: 20px;
  line-height: 30px;
}

@media (min-width: 750px) {
  .class3 {
    font-size: 16px;
    line-height: 22px;
  }
}

// output

.class {
  margin: -3.125vw 0.5vh;
  padding: 5vmin 2.96875vw 1px;
  border: 0.9375vw solid black;
  border-bottom-width: 1px;
  font-size: 4.375vw;
  line-height: 6.25vw;
}

.class2 {
  border: 1px solid black;
  margin-bottom: 1px;
  font-size: 6.25vw;
  line-height: 9.375vw;
}

@media (min-width: 234.375vw) {
  .class3 {
    font-size: 5vw;
    line-height: 6.875vw;
  }
}
```

### Example

```js
'use strict';

var fs = require('fs');
var postcss = require('postcss');
var pxToViewport = require('..');
var css = fs.readFileSync('main.css', 'utf8');
var options = {
  replace: false
};
var processedCss = postcss(pxToViewport(options)).process(css).css;

fs.writeFile('main-viewport.css', processedCss, function(err) {
  if (err) {
    throw err;
  }
  console.log('File with viewport units written.');
});
```

### Options

Default:

```js
{
  viewportWidth: 750,
  viewportHeight: 1334,
  unitPrecision: 3,
  viewportUnit: 'vw',
  selectorBlackList: ['.ignore'],
  minPixelValue: 1,
  mediaQuery: false,
  exclude:/(\/|\\)(node_modules)(\/|\\)/,
}
```

- `viewportWidth` (Number) 视窗的宽度，对应的是我们设计稿的宽度，一般是 750
- `viewportHeight` (Number) 视窗的高度，根据 750 设备的宽度来指定，一般指定 1334，也可以不配置。
- `unitPrecision` (Number) 指定`px`转换为视窗单位值的小数位数（很多时候无法整除）。
- `viewportUnit` (String) 指定需要转换成的视窗单位，建议使用 vw
- `selectorBlackList` (Array) 指定不转换为视窗单位的类，可以自定义，可以无限添加,建议定义一至两个通用的类名。
  - 如果 value 是 string，则检查 selector 是否包含字符串。
    - `['body']` 将会匹配 `.body-class`
  - 如果 value 是 regexp，它会检查选择器是否与正则表达式匹配。
    - `[/^body$/]` 将会匹配 `body` 并不是 `.body`
- `minPixelValue` (Number) 小于或等于`1px`不转换为视窗单位，你也可以设置为你想要的值。
- `mediaQuery` (Boolean) 允许在媒体查询中转换`px`
- `exclude` (RegExp) 排除路径不参与转换

### Use with gulp-postcss

```js
var gulp = require('gulp');
var postcss = require('gulp-postcss');
var pxtoviewport = require('postcss-px-to-viewport');

gulp.task('css', function() {
  var processors = [
    pxtoviewport({
      viewportWidth: 750,
      viewportUnit: 'vmin'
    })
  ];

  return gulp
    .src(['build/css/**/*.css'])
    .pipe(postcss(processors))
    .pipe(gulp.dest('build/css'));
});
```
