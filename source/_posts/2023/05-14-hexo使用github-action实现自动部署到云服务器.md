---
layout: post
title: hexo使用github action实现自动部署到云服务器
date: 2023-05-14 13:11:46
tags:
---

使用 github action 脚本，当我们把 hexo 文章源文件上传至 github 时，会自动帮我们打包，如果你开启 page 功能，那么将会自动 push 至所指定的分支上，实现自动化部署功能  
同理，也可以实现将编译好的 html 文件上传至服务器

## 编译 Hexo
1. 首先创建一个仓库，你可以设置为私有或者公有
2. 进入仓库主页，点击 Actions，点击`New workflow`创建一个名为`build.yml`的 action
![./2023-05-14-14-00-59.png](2023-05-14-14-00-59.png)
3. 配置 hexo 编译 action 脚本  
这里用到的是[`peaceiris/actions-gh-pages@v3`](https://github.com/peaceiris/actions-gh-pages/tree/v3/)的脚本，有需要的可以直接找 hexo 的部署脚本

``` yaml
name: Build # action name

on:
  push:
    branches:
      - master # 当 master 分支发生 push 请求时执行

jobs:
  pages:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js 16.x
        uses: actions/setup-node@v2
        with:
          node-version: "16"
      - name: Cache NPM dependencies
        uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Push To gh-pages
        # https://github.com/peaceiris/actions-gh-pages/tree/v3/
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}  # 无需配置，由 github 提供
          publish_dir: ./public                      # 将指定目录下的文件 push 至 gh-pages
          commit_message: ${{ github.event.head_commit.message }}
```

4. 拉去项目至本地，创建 hexo 博客  
然后随便写一篇文章 push 至`master`分支
5. 查看运行结果  
回到仓库主页，点击 Actions，我们可以看到名为`build`的任务正在执行  
点击正在执行的 action，可以查看日志和目前的进度，等任务走完就可以在`branch`中看到`gh-pages`分支了

## 部署
以上的脚本还只是将我们写好的 hexo 博客，编译打包到了`gh-pages`分支上  
如果是用`github pages`服务的话，只需要配置好仓库名称即可，具体可以搜索参考其他文章，这里主要讲怎么使用`workflow`的形式部署至云服务器上
1. 同样创建一个新的 Action，名称为`deploy.yml`
2. 配置`Deploy`的脚本
``` yaml
name: Deploy

on:
  workflow_run:
    workflows: [Build]
    branches: [master]
    types:
      - completed

jobs:
  pages:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout    
        uses: actions/checkout@v3  # 这里使用了github官方提供的action,checkout项目到虚拟机上
        with:
          ref: gh-pages # 检出 gh-pages 分支下的内容

      - name: Deploy file to tencent
        uses: wlixcc/SFTP-Deploy-Action@v1.0 
        with:  
          username: '${{ secrets.SERVER_USER }}'       # ssh user name
          server: '${{ secrets.SERVER_HOST }}'         # 服务器地址
          ssh_private_key: ${{ secrets.PRIVITE_KEY }}  # 引用之前创建好的 secret
          local_path: './*'                            # 对应 hexo build 后的文件夹
          remote_path: '${{ secrets.TARGET_PATH }}'    # 远程服务器路径
``` 
`workflow_run`中配置了，只有在`master`分支上运行名为`Build`且完成才执行  

3. 创建 openssh key  
如果不需要ssh key登录的，可以参考文档使用账号密码登录，参考第五步配置`secrets`  
首先通过`ssh-keygen.exe`创建密钥对，`-f`指定创建的公私钥存放位置
``` shell
ssh-keygen -t rsa -C "密钥备注" -f ~/.ssh/hexo
# 复制公钥
cat ~/.ssh/hexo.pub
```
执行命令后会在`~/.ssh/`目录下创建两个文件，其中以`.pub`结尾的是公钥，没有的是私钥
4. ssh 登录至服务器，将复制的公钥粘贴至`authorized_keys`文件内
``` shell
# 创建 .ssh 文件夹
cd ~/.ssh
# 创建文件
vim authorized_keys
```
5. 配置仓库 Secrets
进入 github 仓库主页，点击`Settings > Secrets and variables > Actions`，在页面内点击`New respostory serect`按钮  
创建私钥，名称`PRIVITE_KEY`，内容就是第四步创建的私钥内容，注意复制到输入框中的时候私钥末尾需要有一个换行，不然会报格式错误  
根据同样的方式将`SERVER_USER`，`SERVER_HOST`，`TARGET_PATH`同样配置好

## 效果
当以上操作均完成后，现在我们在本地写完文章后只需要把源文件 push 至 github，剩下的都交给 action 处理就好了  
如果 action 执行出错是会发邮件提醒的，点击 action 日志，我们也可以查看是哪里出了错误，来进行对照修改

## 补充
如果有其他需求，如变更 build 后 push 的分支，切换事件分支等，可以参考[**官方文档**](https://docs.github.com/en/actions/using-workflows/triggering-a-workflow)和每个 action 的文档