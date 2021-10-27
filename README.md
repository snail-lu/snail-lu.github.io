# 个人站点项目
### 常用命令
- 安装hexo：
```bash
$ npm install -g hexo-cli
```
- 安装hexo-deployer-git（项目根目录下安装，部署使用）：
```bash
$ npm install hexo-deployer-git --save
```
- 启动本地服务（开发用）：
```bash
$ hexo server
```
- 一键部署（在`main`分支上操作）：
```bash
$ hexo deploy
```
- 如果更换了主题没有生效，可以先清除代码再部署：
```bash
$ hexo clean && hexo deploy
```
- 创建文章(post)/页面(page)/草稿(draft)
```bash
$ hexo new [layout] <filename>
```
- 发布草稿
```bash
$ hexo publish draft <filename>
```

### 分支管理
`main`分支为项目主分支，该分支上的代码无法直接部署到`Github Pages`上。`gh-pages`分支为站点分支，该分支上的代码即为`Github Pages`站点上的静态文件。由`main`分支执行`hexo deploy`而来。

### 文章图片使用CDN
本博客内的文章图片都存放在了`github`上的`imageGalleries`仓库里，直接在博客内引用图片链接，大多数时间图片都是无法正常显示的，需要FQ才行，为了解决这个问题，
改用了`jsdelivr`的CDN进行访问加速。
格式:  `https://cdn.jsdelivr.net/gh/[用户名]/[仓库名]/[图片路径]`
```
// 原始图片链接1
https://raw.githubusercontent.com/Snail-Lu/imageGalleries/main/logo/avatar.png

// 原始图片链接2
https://github.com/Snail-Lu/imageGalleries/blob/main/logo/avatar.png?raw=true

// jsdelivr格式
https://cdn.jsdelivr.net/gh/Snail-Lu/imageGalleries/logo/avatar.png

```

### 相关文档
[1. hexo文档](https://hexo.io/zh-cn/docs/commands)  
[2. hexo-theme-matery文档](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md)  
[2. fontawesome图标](https://fontawesome.com/v5.15/icons?d=gallery&p=2)  