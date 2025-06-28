# Obsidian+Hugo+github+cloudflare 工作流

## 1. hugo 本地构建
参考文献[主题文档 - 基本概念 - LoveIt](https://hugoloveit.com/zh-cn/theme-documentation-basics/#site-configuration)
### 1.1 hugo 安装
因为我是 macOS，所以使用
```zsh
brew install hugo 
```
brew 安装
```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)" 
```
或使用清华源[homebrew](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/)进行安装
### 1.2 创建项目
```zsh
hugo new site my_website      
cd my_website  
```
此处`my_website`为项目名称
### 1.3 安装主题
我使用的主题为[GitHub - dillonzq/LoveIt](https://github.com/dillonzq/LoveIt)
为了后期便于上传 github，所以此处要将你的项目初始化为 git 仓库, 并且把主题仓库作为你的网站目录的子模块:
```zsh
git init          
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt -b v0.3.0  
```
此处指定分支为 `v0.3.0` 截止于 2025 年 6 月 28 日为目前最新稳定版本，主线版本可能需要指定 hugo 版本为 0.145.0 .
#### 1.3.1 使用示例文件
将项目中 `themes/LoveIt/exampleSite` 中的所有文件复制到项目文件夹中。
### 1.4 在本地启动网站
使用以下命令启动网站:
```zsh
hugo serve  
```
在 `http://localhost:1313` 中就能查看你的项目.如果需要修改内容，请在 `content/posts` 和 `hugo.toml` 中修改,具体修改办法详情[主题文档 - 基本概念 - LoveIt](https://hugoloveit.com/zh-cn/theme-documentation-basics/#site-configuration)
## 2. 上传项目至github
两种上传方案，一种是使用 github desktop 客户端上传整个项目，第二种是 github 中新建项目,然后在本地项目文件夹中打开命令行输入：
```zsh
git remote add origin https://github.com/{用户名}/{项目名}.git
git add .
git commit -m "first commit"
git branch -M main  
git push -u origin main  
```
## 3. github action 自动更新
在 `.github/workflows` 中中创建一个 `hugo.yml`，内容如下：
```yaml
name: GitHub Pages

on:
  push:
    分支:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-latest
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: '0.145.0'
          extended: true
          
      - run: git pull
      
      - name: Build
        run: hugo --minify
      - run: |
             git config user.name "github-actions[bot]"
             git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
             git add ./public
             git commit -m "generated"
             git push
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```
然后在项目设置中找到 `action` 项，选择通用，划到最下方工作流权限并给予读写权限。
好了，现在修改`main`中的文件便会触发博客的自动更新。
## 4. 上传cloudflare  
在登录 cloudflare [Cloudflare Dashboard \| Manage Your Account](https://dash.cloudflare.com/)后选择 Workers 点击创建；选择导入现有 git 仓库，选择刚才创建的仓库，下一步，然后将构建设置中的根目录设置为`public`,即可完成部署。
如果自己有域名的可在自定义域中添加自己的域名。
## 番外：快速构建博客
参考来源[使用Obsidian优雅地写Hugo博客文章 \| 梓言堂 - Yuk's Blog](https://blog.yuk7.com/posts/obsidian-hugo/)

使用到以下插件：
- [Enveloppe](https://github.com/Enveloppe/obsidian-enveloppe)
- [Remotely Save ](https://github.com/remotely-save/remotely-save)
- [Templater](https://github.com/SilentVoid13/Templater)
- [Auto Link Title](https://github.com/zolrath/obsidian-auto-link-title)
- [Image Converter](https://github.com/xryul/obsidian-image-converter)
- [auto upload image with picgo](https://github.com/renmu123/obsidian-image-auto-upload-plugin)

参考资料：

