# webpack

## 在 v4 升级到 v5 的时候遇到过哪些问题

- 项目里面自己写的插件不兼容，需要改变一下写法。
- 配置的写法不兼容了，一些写法的改变
- css loader 默认开启了 esm，需要回退到 cjs
- 开启了持久化缓存 编译速度大大提神
- 改写了预发流水线的部署脚本，只有当 package json 或者 lock 文件发生变化的时候，才会重新安装依赖并且删除缓存，提升了项目发布到预发环境测试的速度。
- node-sass 和 thread-loader 结合使用的时候，线程只能设置 2 个。

## 什么是 pitch loader

简单来讲，就是普通的 loader 从下往上执行，pitch loader 从上往下执行

`style-loader`就是典型的 pitch loader. Why？

https://github.com/webpack-contrib/style-loader/issues/567

因为 style-loader 不需要知道它的前一个 loader 是谁，也就是说它不需要对 css-loader 传递来的数据进行特殊转换，它和什么 loader 配合都可以。只需要请求转换好的数据就行了。

## 性能优化

使用 webpack 可以进行哪些性能优化。

1. splitChunks. 拆包，合理的拆包可以让项目的总 js 大小更小。
2. DLL。 像 pdd 内部有统一的 dll 公共包，在页面之间跳转的时候，dll 文件的 js 可以复用。

## webpack 热更新

当你修改文件的时候,webpack 开发服务器会通过 web socket 会浏览器发送通知，让浏览器发请求来获取文件 chunk 的增量改动。

当我们编写 webpack loader 时候，一般需要对资源进行热更新的处理。比如

```js
// eg: iso-style-loader
if (module.hot && typeof window !== 'undefined' && window.document) {
    var removeCss = function() {};
    module.hot.accept(${stringifyRequest(this, `!!${request}`)}, function() {
    css = require(${stringifyRequest(this, `!!${request}`)});
    content = typeof css === 'string' ? [[module.id, css, '']] : css;
    removeCss = insertCss(content, { replace: true });
    });
    module.hot.dispose(function() { removeCss(); });
}
```

## 什么是tapable

tapable是webpack依赖的一个库，用来进行发布订阅，在我们自定义webpack插件的时候经常用到。