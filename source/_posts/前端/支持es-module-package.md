---
layout: nestjs
title: 支持es-module-package
date: 2022-03-16 14:01:10
tags: 
  - nest
  - nestjs
categories: 
  - nest
  - nestjs
readtime: true
---

---
highlight: a11y-light
---
由于社区许多npm包采用esm(es module)写之后 项目需要兼容esm和cjs(commonjs) 两种包结构
<!--more-->
使用@nest/cli的创建的nestjs项目会使用webpack-node-externals将node_modules包排出转换cjs引入打包之后会转换成这样
```
module.exports = require("@nestjs/common");
```

esm 的包也会转换为
```
module.exports = require("parse-json");
```

我们需要把esm的包转换为cjs才能使用

我们直接把esm打包文件里转换为cjs就可以使用了
直接覆盖nestjs的默认webpack配置文件
// webpack.config.js
```
const nodeExternals = require('webpack-node-externals');
module.exports = function (options, webpack) {
  return {
...options,
 externals: [
      nodeExternals({
        allowlist: [/^parse-json/i],
      }),
    ],
}
    
```
