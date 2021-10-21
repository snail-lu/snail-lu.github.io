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


### 分支管理
`main`分支为项目主分支，该分支上的代码无法直接部署到`Github Pages`上。`gh-pages`分支为站点分支，该分支上的代码即为`Github Pages`站点上的静态文件。由`main`分支执行`hexo deploy`而来。

### 相关文档
[1. hexo文档](https://hexo.io/zh-cn/docs/commands)  
[2. hexo-theme-matery文档](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md)  
[2. fontawesome图标](https://fontawesome.com/v5.15/icons?d=gallery&p=2)  