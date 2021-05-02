---
title: 这个博客是如何搭建的？
date: 2021-03-28 15:49:08
tags:
- 博客
description:  整个搭建过程基于B站的教学视频《Luke教你20分钟快速搭建个人博客系列(hexo篇)》。此外，默认配置下博客的主题会有一些细节问题，文本还添加了一些自己的博客、主题配置记录，让博客更完整。比如，导航的『关于』等按钮，可以设置显示或隐藏；显示的话，还要添加『关于』页面。
---

整个搭建过程基于B站的教学视频《[Luke教你20分钟快速搭建个人博客系列(hexo篇)](https://www.bilibili.com/video/BV1dt4y1Q7UE)》。此外，默认配置下博客的主题会有一些细节问题，文本还添加了一些自己的博客、主题配置记录，让博客更完整。比如，导航的『关于』等按钮，可以设置显示或隐藏；显示的话，还要添加『关于』页面。
## 一、基本创建

```powershell
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

启动服务后，访问指定地址，可以看到博客。

### 基本配置

`站点配置文件`是`_config.yml`，其`#site`部分，可以修改博客的信息、标题、作者名和语言等。

### 更换皮肤

- 在GitHub里搜hexo-theme来寻找受欢迎的主题，比如说用[cactus](https://github.com/probberechts/hexo-theme-cactus)。  
  - 如果是第一次搭建博客，可以先别急着找心仪的主题，把一切跑通，知道大概怎么玩了，再慢慢更换为自己喜欢的主题。
- 下载并放到theme目录下。
- 修改`站点配置文件`中`theme`字段。
- 主题目录下还有个同名的`_config.yml`文件，是`站点配置文件`，可以配置主题色等，**本文最后部分有简单介绍**。

> 现在主题现在换用为[NexT](https://github.com/theme-next/hexo-theme-next)了。

## 二、部署到GitHub pages

- 在GitHub上创建项目，项目名称为：`[用户名].github.io`
- 当前项目的根目录下执行`git init`
- 指定远程地址到该GitHub项目：`git remote add origin 远程地址`
- 安装yarn工具：`npm i -g yarn`
- 安装一个库，它会帮助我们将生成好的代码部署到一个具体分支：`yarn add hexo-deployer-git`
- 在`站点配置文件`中添加部署配置

    ```powershell
    deploy:
      type: git
      repo: 博客的GitHub地址
      branch: master
    ```

- 运行`npm run deploy`

    在GitHub项目页 - Settings - GitHub Pages，上边会显示已经部署到`[用户名].github.io`。访问地址可以看到博客。

## 三、自动化部署

利用GitHub Actions

### push到远程

因为master目录已经被占用了，所以需要把代码提交到另一个分支。

- 先commit当前修改。`git add .`  然后 `git commit -m '...'`。
- 添加新的分支，这里分支名为myblog。`git branch myblog`。
- 切换分支：`git checkout myblog`。
- 指定到远程分支：`git push --set-upstream origin myblog`。

### 创建自动部署文件

在根目录下创建`.github`目录并在其中创建`workflows`目录，然后在workflows目录中创建`deploy.yml`文件，填入以下内容

```powershell
name: Build and Deploy
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v2 # If you're using actions/checkout@v2 you must set persist-credentials to false in most cases for the deployment to work correctly.
        with:
          persist-credentials: false
      - name: Install and Build 🔧 # This example project is built using npm and outputs the result to the 'build' folder. Replace with the commands required to build your project, or remove this step entirely if your site is pre-built.
        run: |
          npm install
          npm run build
        env:
          CI: false
      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@releases/v3
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          BRANCH: master # The branch the action should deploy to.
          FOLDER: public # The folder the action should deploy.
```

### 提交代码并push

```cmd
git add .
git commit -m '...'
git push —set-upstream origin myblog
```

推送到myblog分支后，就触发更新文章master分支，但需要等一会。

## 四、在线编辑文章

### 思路

在GitHub中可以直接修改对应markdown源文件，然后会自动触发action。因此在任何地方只要登录了GitHub账号，就可以去到项目中修改文章了。

### 给文章添加快速跳转

某个文件的修改的地址是：[https://github.com/用户名/用户名.github.io/edit/分支名/source/_posts/文件名.md](https://github.com/ratiger/ratiger.github.io/edit/myblog/source/_posts/hello-world.md)。
在博客文章模板中添加可以文件路径，这样可以直接跳转到这个地方，进而编辑。
在`theme/你的主题/layout/`中下的某个模板文件的预想的位置，比如我的是在`footer.ejs`里，添加链接：

```html
<a href="https://github.com/用户名/用户名.github.io/edit/分支名/source/<%- page.source %>" target="_blank">编辑</a>
```

可以先本地启动服务看看效果，决定后再push到远程分支。

## 五、编辑

### 添加文章

`hexo new post "文件名"`

### 完成『关于』

添加关于页面，`hexo new page about`，然后在这个页面添加内容。

### 完成『标签』

#### 添加标签导航页

`hexo new page tags`，会生成source/tags/index.md，然后在开头部分添加`type: tags`，然后在配置文件的nav中添加`tag: /tags/`。

#### 在页面中添加标签

在文章开头，添加如下内容：

```cmd
tags:
- 标签1
- 标签2
```

## 六、配置主题

`主题配置文件`可以配置与该主题相关的内容。比如，导航和社交链接等，注释掉不要的，。或者开启并添加自己的链接。

每个主题都有自己的配置方式。next的[配置文档](http://theme-next.iissnan.com/getting-started.html)

1. 『social』：可以改变左边导航中的显示或隐藏『邮件』、『GitHub』等链接图标，还可以添加自己的，还可以添加自己的，比如『知乎』等，图标就要自己找咯。
2. 『Schemes』：主题之下又分几种风格，可以在这里选择用哪个。还可以开启夜间模式。
3. 『menu』：可以配置导航栏的菜单按钮。
4. 修改字体大小：该主题目录下的`source/css/_variables/base.styl`的`$font-size-base`，默认是`16px`的。
5. 添加摘要：[如何设置「阅读全文」？](http://theme-next.iissnan.com/faqs.html#read-more)。
6. 修改文案：配置文件在主题目录的`languages/zh-CN.yml`中。(其实在项目目录中搜中文关键词更快。)

> `主题配置文件`里的`fa-github`之类的图标用的是[Font Awesome](https://www.thinkcmf.com/font_awesome.html) 。如果想换其他图标的话可以去网站里看看。

## 参考

- B站视频 [Luke教你20分钟快速搭建个人博客系列(hexo篇)](https://www.bilibili.com/video/BV1dt4y1Q7UE)
  - 课代表：[搭建个人博客 - hexo](https://www.jianshu.com/p/97dfbc8e79db)
- [probberechts/hexo-theme-cactus](https://github.com/probberechts/hexo-theme-cactus) 各种配置和用法