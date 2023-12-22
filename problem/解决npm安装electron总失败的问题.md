# 解决npm安装electron总失败的问题

## 前言

也是久闻Electron大名，而且新版QQ最近也用Electron改写了。正好最近想写一个图形化的程序，无奈安装的时候老是卡住。还好网上早就有人遇到了跟我一样的问题，也给出了方案。

## 正文

转自：https://zhuanlan.zhihu.com/p/562217675

### 总述解决办法

先运行

```text
npm install -g cnpm --registry=https://registry.npmmirror.com
```

再运行

```text
cnpm install --save-dev electron
```

### 解决过程

今天在学习electron的quick start教程时到安装electron的步骤`npm install --save-dev electron`总是不成功.会卡在`reify:lodash: timing reifyNode:node_modules/@types/node Completed in 578ms`这里。

百度和谷歌搜索，各种换源加代理都没解决。 最后找到了`https://npmmirror.com/`这个网站，根据指导，安装定制的cnpm管理工具`npm install -g cnpm --registry=https://registry.npmmirror.com`

然后执行`cnpm install --save-dev electron` 成功了。

