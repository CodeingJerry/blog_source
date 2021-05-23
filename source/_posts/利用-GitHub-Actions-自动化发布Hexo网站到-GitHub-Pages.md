---
title: 利用 GitHub Actions 自动化发布Hexo网站到 GitHub Pages
date: 2021-05-23 19:31:14
tags: GitHub Actions,Hexo
categories: 效率工具
---
### 介绍
GitHub+Hexo 搭建个人博客非本文重点，请自行搜索。
成功搭建博客以后，每次写完文章，通过执行 `hexo clean && hexo g -d` 来发布，当你文章比较多的时候，可能还需要等待很久，而且还可能会遇到本地安装的 `Node.js` 版本与 `Hexo` 不兼容的问题，利用 `GitHub Actions` 自动部署 `hexo` 到 `Github Pages`，只需在文章写完后运行`git push origin master`命令，等待20秒，文章自动发布成功。
<!--more-->
### 前提
1. 存放代码仓库 `blog_source`
2. 存放静态博客页面仓库 `CodeingJerry.github.io`
### 准备密钥
1. 电脑上生成密钥
   `ssh-keygen -t rsa -C "这里换上你的邮箱" # 三次回车即可`，在指定的保存路径下会生成2个名为id_rsa和id_rsa.pub的文件。
2. 打开 https://github.com/CodeingJerry/blog_source/settings/secrets 点击 New repository secret，分别在  Name 输入 `WIN7_HEXO_DEPLOY_PRI`，  Value 输入前面生成的`id_rsa` 的内容。
3. 打开你的`github`，进入配置页： Settings -- SSH and GPG keys，选择`New SSH key`，然后用文本工具打开之前生成的`id_rsa.pub`文件，把内容拷贝到key下面的输入框，并为这个key定义一个名称（通常用来区分不同主机），然后保存。
### Hexo 配置
在项目根目录中修改 `_config.yml`
```
deploy:
  type: git
  repo: git@github.com:CodeingJerry/CodeingJerry.github.io.git
  branch: master
```
### 编写 GitHub Actions
在 blog_source 仓库根目录下创建 .github/workflows/deploy.yml 文件，目录结构如下。
```
blog_source (repository)
└── .github
    └── workflows
        └── deploy.yml
```
在 deploy.yml 文件中粘贴以下内容，注释可以删除。
```
name: Deploy Hexo Blog # 此 Action 的名字

on:
  push:
    branches:
      - master # 当 master 分支有新push时运行

env: # 定义环境变量
  GIT_USER: CodeingJerry
  GIT_EMAIL: 895277169@qq.com
  THEME_REPO: theme-next/hexo-theme-next
  THEME_BRANCH: master
  DEPLOY_REPO: CodeingJerry/CodeingJerry.github.io
  DEPLOY_BRANCH: master

jobs:
  build: # 一项叫做build的任务
    name: Build on node ${{ matrix.node_version }} and ${{ matrix.os }}
    runs-on: ubuntu-latest # 在最新版的Ubuntu系统下运行
    strategy:
      matrix:
        os: [ubuntu-latest]
        node_version: [12.x]

    steps:
      - name: Checkout # 将仓库内master分支的内容下载到工作目录
        uses: actions/checkout@v2 # 脚本来自 https://github.com/actions/checkout

      - name: Checkout theme repo # 将theme-next/hexo-theme-next仓库内master分支的内容下载到themes/next
        uses: actions/checkout@v2
        with:
          repository: ${{ env.THEME_REPO }}
          ref: ${{ env.THEME_BRANCH }}
          path: themes/next

      - name: Checkout deploy repo # 将CodeingJerry/CodeingJerry.github.io仓库内master分支的内容下载到.deploy_git
        uses: actions/checkout@v2
        with:
          repository: ${{ env.DEPLOY_REPO }}
          ref: ${{ env.DEPLOY_BRANCH }}
          path: .deploy_git

      - name: Use Node.js ${{ matrix.node_version }} # 安装Node
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node_version }}

      - name: Configuration environment
        env:
          HEXO_DEPLOY_PRI: ${{secrets.WIN7_HEXO_DEPLOY_PRI}}
        run: |
          sudo timedatectl set-timezone "Asia/Shanghai" # 设置时区
          mkdir -p ~/.ssh/
          echo "$HEXO_DEPLOY_PRI" > ~/.ssh/id_rsa # 配置秘钥
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name $GIT_USER
          git config --global user.email $GIT_EMAIL
          cp _config.theme.yml themes/next/_config.yml # 自定义主题配置文件
      - name: Install dependencies
        run: |
          npm install # package.json的devDependencies字段里的插件会被自动安装到node_modules下
      - name: Deploy hexo
        run: |
          npm run deploy # 执行package.json中的deploy命令即hexo deploy 
```
### 配置文件
复制一份主题配置文件，重命名为 _config.theme.yml，放置blog_source根目录。
最终目录结构
```
blog_source (repository)
├── _config.theme.yml
└── .github
    └── workflows
        └── deploy.yml
```
把 _config.theme.yml 与 deploy.yml 文件推送到 blog_source 仓库，在此仓库 Actions 页面可以看到一个名字为 Deploy Hexo Blog 的 Action。
### 执行任务
写一篇文章，push 到 blog_source 仓库的 master 分支，在此仓库 Actions 页面查看当前 task。
<img src="/images/github_actions/github_actions.png" align="center">
查看自己的博客，不出意外的话已经可以看到新添加的文章了。
### 参考
[利用 Github Actions 自动部署 Hexo 博客](https://sanonz.github.io/2020/deploy-a-hexo-blog-from-github-actions/)
[如何正确的使用 GitHub Actions 实现 Hexo 博客的 CICD](https://hdj.me/github-actions-hexo-cicd/)
[GitHub Actions 入门教程](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)
[npm scripts 使用指南](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)

