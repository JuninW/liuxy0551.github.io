---
title: 前端项目优化之旅（二）—— Vant 自动按需引入组件
urlname: vue-cli3-vant-load-on-demand
tags:
  - Vue
  - Vant
categories:
  - Vue
  - Vant
author: liuxy0551
copyright: true
date: 2019-12-03 11:21:21
updated: 2019-12-03 11:21:21
---

## 介绍

　　`前端项目优化之旅`系列随笔主要记录工作中遇到的一些优化方案，这里记录一下 Vue CLI 3 项目中如何让 Vant 自动按需引入组件
<!--more-->


### 一、安装 Vant

``` shell
npm i vant -S
```


### 二、完整引入

　　在 main.js 中写入以下内容：

``` javascript
import Vant from 'vant'
import 'vant/lib/index.css'
Vue.use(Vant)
```
![](/images/posts/vue-cli3-vant-load-on-demand/1.png)


### 三、自动按需引入组件`推荐`

　　1、[babel-plugin-import](https://github.com/ant-design/babel-plugin-import) 是一款 babel 插件，它能在编译的过程中将 import 写法自动转换为按需引入的方式。

``` shell
npm i babel-plugin-import -D
```

　　2、babel 7 在`babel.config.js`中配置

``` javascript
module.exports = {
  // 自动按需引入 Vant 组件
  plugins: [
    ['import', { libraryName: 'vant', libraryDirectory: 'es', style: true }, 'vant']
  ]
}
```

　　3、在`src/components`下新建`vant.js`文件，具体代码见 [src/components/vant.js](https://github.com/liuxy0551/vue-cli3-build-optimization/blob/master/src/components/vant.js)

``` javascript
// 自动按需引入 Vant 组件
import { Tabbar, TabbarItem, Button } from 'vant'
let vantUIs = [Tabbar, TabbarItem, Button]

export default {
  install (Vue) {
    vantUIs.forEach(vantUI => [
      Vue.component(vantUI.name, vantUI)
      // Vue.use(vantUI)    // 也可以使用 Vue.use()
    ])
  }
}
```

　　4、在`main.js`中引入`src/components/vant.js`：

``` javascript
import vant from './components/vant'
Vue.use(vant)
```

　　5、页面使用组件：

``` javascript
<van-button type="default">默认按钮</van-button>
```
![](/images/posts/vue-cli3-vant-load-on-demand/2.png)


- 主要体现在 vant 文件大小减少了约`384 KB`