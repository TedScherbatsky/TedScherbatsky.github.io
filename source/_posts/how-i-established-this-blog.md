---
title: 这个博客是如何搭建的？
date: 2021-03-28 15:49:08
tags:
- 博客
- 一步步记录
---

整个搭建过程完全参照B站视频《[Luke教你20分钟快速搭建个人博客系列(hexo篇)](https://www.bilibili.com/video/BV1dt4y1Q7UE)》，这里是跟着视频一步步做，并且把每一步记录下来而已。

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

在`_config.yml`文件`#site`部分，可以修改博客的信息、标题、作者名和语言等。

### 更换皮肤

- 在GitHub里搜hexo-theme来寻找受欢迎的主题，我的是[cactus](https://github.com/probberechts/hexo-theme-cactus)
    - 如果是第一次搭建博客，可以先别急着找心仪的主题，把一切跑通，知道大概怎么玩了，再慢慢更换为自己喜欢的主题。
- 下载并放到theme目录下
- 修改`_config.yml`中`theme`字段
- 该主题的`_config.yml`文件的`colorscheme`字段可以改主题色

## 二、部署到GitHub pages

- 在GitHub上创建项目，项目名称为：`[用户名].github.io`
- 当前项目的根目录下执行`git init`
- 指定远程地址到该GitHub项目：`git remote add origin 远程地址`
- 安装yarn工具：`npm i -g yarn`
- 安装一个库，它会帮助我们将生成好的代码部署到一个具体分支：`yarn add hexo-deployer-git`
- 在`_config.yml`中添加部署配置

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
- 先commit当前修改。`git add .`  然后 `git commit -m '...'`
- 添加新的分支，这里分支名为myblog。`git branch myblog`
- 切换分支：`git checkout myblog`
- 指定到远程分支：`git push --set-upstream origin myblog`

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

```
git add .
git commit -m '...'
git push —set-upstream origin myblog
```
推送到myblog分支后，就触发更新文章master分支，但需要等一会。

## 四、在线编辑文章

### 思路
在GitHub中可以直接修改对应markdown源文件，然后会自动触发action。因此在任何地方只要登录了GitHub账号，就可以去到项目中修改文章了。

### 给文章添加快速跳转

某个文件的修改的地址是：[https://github.com/用户名/用户名.github.io/edit/分支名/source/_posts/文件名.md](https://github.com/TedScherbatsky/TedScherbatsky.github.io/edit/myblog/source/_posts/hello-world.md)。在博客文章模板中添加可以文件路径，这样可以直接跳转到这个地方，进而编辑。
在`theme/你的主题/layout/`中下的某个模板文件的预想的位置，比如我的是在`footer.ejs`里，添加链接：
```html
<a href="https://github.com/用户名/用户名.github.io/edit/分支名/source/<%- page.source %>" target="_blank">编辑</a>
```
可以先本地启动服务看看效果，决定后再push到远程分支。

## 五、其他

### 配置导航
在主题目录的`_config.yml`中，有导航链接`nav`和社交链接`social_links`，可以配置自己的链接，或者注释掉不要的项。

### 添加文章
`hexo new post "文件名"`

### 完成『关于』
添加关于页面，`hexo new page about`，然后在这个页面添加内容。

### 完成『标签』
#### 添加标签导航页
`hexo new page tags`，会生成source/tags/index.md，然后在开头部分添加`type: tags`，然后在配置文件的nav中添加`tag: /tags/`。
#### 在页面中添加标签：
在文章开头，添加如下内容：
```
tags:
- 标签1
- 标签2
```

## 参考

- B站视频 [Luke教你20分钟快速搭建个人博客系列(hexo篇)](https://www.bilibili.com/video/BV1dt4y1Q7UE)
    - 课代表：[搭建个人博客 - hexo](https://www.jianshu.com/p/97dfbc8e79db)
- [probberechts/hexo-theme-cactus](https://github.com/probberechts/hexo-theme-cactus) 各种配置和用法