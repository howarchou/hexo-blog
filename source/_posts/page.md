---
title: Hexo安装过程

date: 2020-04-15 10:21:03

tags: 
- hexo
- blog

categories:
- hexo
---

### 1、GitHub创建个人仓库

注册github账号，登录后，Create a new repository，输入repository name， 完整的仓库名格式：<user-name>/<repository-name>.github.io 格式，user-name为github的账号名，repository-name为仓库名。

### 2、全局安装hexo
打开cmd，输入如下命令：

```
npm install -g hexo
```

### 3、创建项目名myblog，并初始化项目
```
// 本地运行
cd myblog
hexo init
npm install 
hexo server 
```

### 4、部署到github
修改根目录下面的_config.yml文件，找到deploy字段，并填写完整，

```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/howarchou/howarchou.github.io.git
  branch: master
```
尤其注意字段后面的空格。然后执行如下命令，即可发布到github。
```
npm install hexo-deployer-git --save
hexo clean
hexo deploy
```
注意 deploy时可能要你输入github的username和password。
### 5、查看效果

浏览器访问：https://howarchou.github.io 即可看到效果。

### 6、个性化域名


1、在项目文件夹下的source目录，新建一个CNAME文件，没有后缀，里面仅一行代码，写上域名， 然后执行：

```
hexo clean
hexo g
hexo d
```

2、去万网购买一个域名并认证，进入dns解析控制台，我用的[dnspod](https://www.dnspod.cn/)，新建两条A记录，分别指向185.199.108.153和185.199.109.153；再增加一条CNAME记录，主机记录名为blog，指向刚才浏览器访问的网址：_howarchou.github.io._  
注意后面有个点，过几分钟dns解析生效后就可以看效果了。比如我的域名是zhougg.com， 访问地址是：blog.zhougg.com 。
至此，搭建的个性博客就完成了。





