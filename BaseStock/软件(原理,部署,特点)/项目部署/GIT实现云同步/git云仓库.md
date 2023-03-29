GIT(云仓库)
特性:速度快.分布式
git与svn的区别
1.svn是一个集中式的版本管理工具,服务器宕机那么整个svn版本不可用
2.git如果一个仓库服务器宕机,不影响其他服务器
GIT默认是VIM语法(同linux)
git init 在当前本地文件夹创建.git文件夹仓库标识
git config --global user.name "账户名"
git config --global user.email "邮箱"
git config --list (查看所有配置信息)
ls -al ~/.ssh(检查全盘电脑是否有SHH秘钥)
git remote -v (查看简要远程服务器信息)
git remote add/rm 远程仓库名称 地址 (添加/删除仓库)
git branch (查看分支)
git branch 分支名 (创建分支)
git checkout 分支名 (切换分支)
git branch -d 分支名 (删除分支)
git merge 分支名 (合并分支)
git fetch //把远程库的代码更新到本地库
git stash //把未完成的修改缓存到栈容器中 git stash list //查看所有的缓存 git stash pop //恢复本地分支到缓存状态 git blame someFile //查看某个文件的每一行的修改记录（）谁在什么时候修改的） git status //查看当前分支有哪些修改 git log //查看当前分支上面的日志信息 git diff //查看当前没有add的内容 git diff --cache //查看已经add但是没有commit的内容 git diff HEAD //上面两个内容的合并 git reset --hard HEAD //撤销本地修改 echo $HOME //查看git config的HOME路径 export $HOME=/c/gitconfig //配置git config的HOME路径
![[3DE0CB7A7FB646178830813B89CDE459.jpg]]

git add/reset 文件名 (将工作区文件加入/删除到暂存区)

git commit 文件名 (将暂存区文件提高到版本库)

git pull/push 仓库名称 分支名称 (拉取/推送到本地)

git clone 远程仓库地址 (克隆远程仓库到本地)

可以直接在 c/user/用户名/.gitconfig中修改

git rm 文件名 (本地仓库删除文件)
git status -s (查看文件状态)

![[BF931032F1134CB8BAFF7B67FE7EB3C5.jpg]]

idea集成GIT

1.下载安装gitbash作为传输工具

网上查找git安装教程(一般使用默认选项安装即可)如果右键没有gitbash可以考虑重装

2.进入idea中VCS选项->improt into version control->create

git respository将指定文件夹设置为git仓库然后将我们需要传输的项目文件夹放入仓库文件夹下

3.书写.ignore文件忽略掉.idea和target和.iml和test等文件

4.右键项目 GIT-> Add(加入暂存区)->Commit Diectory(提交到本地仓库)->push(推到远程仓库)

5.commit时会弹出设置username和邮箱,自定义设置(可以按gitee规范输入,后续也可以通过git config -list查看和修改)

6.第一次提交需要定义origin(原始)地址(输入gittee上的地址)

第一次还需要输入gitee的账号密码通过,然后提交到远程仓库成功

git忽略列表设置方法

我们可以在工作目录中创建一个名为 .gitignore 的文件（文件名称固定），列出要忽略的文件模式。

可以输入指令 git vim .gitignore来创建该文件

# 后缀名为.a的文件忽略

*.a

# lib.a文件不忽略

!lib.a

# 只忽略文件名为TODO的文件

/TODO

# 忽略build文件夹下的所有文件

build/

# 忽略doc文件夹下的txt文件

doc/*.txt

# 忽略doc文件夹及其子文件夹中的后缀名为pdf的文件

doc/**/*.pdf

关于SSH秘钥

1.SSH代理转发(最常用) SSH部署到SSH-agent

2.使用 OAuth 令牌进行 HTTPS 克隆

3.部署密钥

4.GitHub App 安装访问令牌

5.机器用户

详情见[https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/managing-deploy-keys](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/managing-deploy-keys)

git本地仓库

本地仓库创建

1.git仓库设置,在自己创建一个文件夹MyGitRepository

然后创建一个repo1的子文件夹,在这里面可以调用git输入命令

git init初始化一个仓库,会生成一个.git文件夹

2.远程git仓库克隆

在MyGitRepository文件夹下调用

远程仓库地址,会克隆一个远程仓库

git clone https://gitee.com/qiuzhiwei1989/MyGitRepository.git

  

git提交到本地仓库顺序

![[5F626B4E601B4740BB7DBB2E02E835EB.jpg]]

必须先提交到暂存区,未在暂存区意味着不在版本控制状态内

加入暂存区常用语法: git add 文件名

加入本地仓库常用语法: git commit 文件名 -m "日志内容"

git commit -m "日志内容"

提交所有文件

git add 文件名

将未跟踪状态的指定文件加入暂存区

git add .

将未跟踪状态的所有文件加入暂存区

git reset 文件名

将指定文件从暂存区移除

git reset

将暂存区的所有文件都移除，文件状态会变为未跟踪状态（）

输入i是insert

exc退出编辑状态

:wq是保存并且推出write&quit

  

  

git工作区中文件的两种状态

untracked(未跟踪)

代表此文件在文件夹中,但并没有加入到git库,不参与版本控制

  

tracked(已跟踪)

Unmodified（未修改状态）

文件未修改, 即版本库中的文件内容与工作区中的文件内容完全一致.

modified（已修改状态）

文件已经修改，即版本库中的文件内容与工作区中的文件内容完全不一致.

Staged（已暂存状态）

文件已经加入到暂存区

  

查看文件状态两种语法

1. git status 查看文件详细状态

2. git status -s 查看文件简单状态

  

注意事项:git status指令查看tracked下的Unmodified状态，不提示！因为文件内容没有发生改变，不需要进行版本管理。

  

删除文件的两种语法

1. git rm 文件名 :删除工作区的文件,并将删除行为添加到暂存区,版本库中的文件并没有删除,需要将本次删除行为从暂存区commit到本地仓库才会执行删除

2. rm 文件名 :删除工作区文件，没有将这次删除放入暂存区，本地仓库中的文件没有删除，需要将删除行为add到暂存区，然后将删除行为commit到本地仓库

(相比于上面的语法,要多一步git add加入缓存区的操作)

  

  

  

git远程仓库

  

查看远程仓库

git remote 显示已经绑定的远程仓库名称 默认是origin

git remote -v 显示详细 地址

添加远程仓库

git remote add 远程仓库名 远程仓库地址

移除远程仓库

git remote rm 远程仓库名

从远程仓库抓取

git pull 远程仓库名 分支名

特殊情况:如果本地仓库中原本就有数据,拉取远程仓库可能会因为内容名相同而被覆盖,这是一种危险操作,

那么需要 设置一个强制允许将远程仓库的内容拉取到本地仓库

git pull 远程仓库名 分支名 --allow-unrelated-histories

  

克隆远程仓库

git clone 远程仓库地址

这个操作包含了,初始化init 绑定远程仓库和拉取远程仓库

  

推送到远程仓库

git push 远程仓库名 分支名

将本地仓库中的内容推送到远程仓库

步骤

1.新建文件 abc...

2.将文件 abc放入暂存区

3.将文件 abc放入本地仓库

3.将文件 abc放入远程仓库

  

4.最后 git push 远程仓库名 分支名

  

git分支

分支的作用:如果有一个新的功能可以在新的分支中操作,然后切换回主分支功能救会合并回来

git branch 查看本地分支

git branch -r查看远程分支

git branch -a查看所有分支

  

  

以master分支为基础创建分支,如果master中有推送的内容,那么创建的新的也会有推送的内容

如果没有对应远程分支那么会自动创建,

  

分支追踪,如果向指定AAA远程仓库推送,则会由本地仓库AAA推送内容,不论是哪个本地仓库输入指令

git branch 分支名 创建分支

git checkout 分支名 切换分支

git push 远程仓库名 分支名 本地分支推送至远程分支

git branch -d 分支名 删除本地分支

git branch -D 分支名 强制 删除本地分支

git push 远程仓库名 -d 远程分支名 删除远程分支

删除注意事项:

1.如果本地分支(master分支删除fuck分支内容)和远程分支

(远程fuck)中内容不一致,那么需要将内容保持一致, 但是也可以直接使用强制删除本地分支

2.如果当前在XXX分支中,则无法删除XXX分支,需要切换到其他分支

  

合并分支

git merge 分支名 将制定分支合并到当前分支中

注意事项:

分支冲突:不同分支中,同时对一个文件内容进行了修改,此时合并就会出现分支冲突问题,

解决: 1.编辑冲突文件 取消冲突符号

2.将编辑之后的文件add到缓存区

3.使用 git commit -m "解决冲突" 来全部提交而不能指定提交