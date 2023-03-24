
1. [下载git for windows](https://gitforwindows.org/)并配置好本地参数(例如在注册表中加入gitbash,默认会帮你加)
2. 到指定本地文件夹打开gitbash,或使用cd命令到指定文件夹使用git init命令生成.
3. git文件夹(标识为本地仓库)git config --list查看参数,然后
git config --global user.name "账户名"
git config --global user.email "邮箱"
git remote add/rm 远程仓库名称 地址 (添加/删除仓库)
git remote -v查看是否配置成功
4. 下载github for windows 便捷完成add commit 和push操作,另外根据需求配置.ignore文件
5. 将需要上传的文件放到指定文件夹内完成add-commit-push操作

### 关于SSH秘钥
1.SSH代理转发(最常用) SSH部署到SSH-agent
2.使用 OAuth 令牌进行 HTTPS 克隆
3.部署密钥
4.GitHub App 安装访问令牌
5.机器用户
详情见[https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/managing-deploy-keys](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/managing-deploy-keys)

###### 代理方式
1. 检查是否有公钥  ls -al ~/.ssh(检查全盘电脑是否有SHH秘钥) 
2. 生成rsa秘钥和公钥(可以随意使用任何rsa的秘钥生成工具或者git bash指令直接生成)
ssh-keygen -t rsa -C "196550181@qq.com"生成新的ssh,(全程回车设置即可)
默认地址c/user/用户名/.ssh  文件夹内一个私钥和一个公钥,
3.  将rsa公钥添加到代码托管平台 (github或gittee)  
4. 将rsa秘钥添加到ssh-agent中，为ssh client指定使用的秘钥文件

fringerprint:
SHA256:76Ng6b2uUPBI/2PEmnccwiAhrBRSIy+zNeqnugAs4Hw 196550181@qq.com



