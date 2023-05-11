**Docker作用**
	**核心概念:每个软件都是以集装箱形式存在完全独立**
	集装箱:将你的应用程序打包成一个集装箱(例如集装箱A:jdk1.8+wfx-search,集装箱B:nginx+gcc+依赖库)，谁需要这个应用直接把整个箱子拿过去即可
	**1.解决不同人员开发环境不一致导致的问题**
	docker会将应用和环境打包成集装箱,不同应用即使环境需求不同他们分别在不同集装箱运行不会影响其他应用(不需要一个统一的环境去兼容多个应用了)
	**2.解决虚拟机环境启动慢和传统搭建服务集群效率低的问题,且占用硬盘大幅减少**
	docker快速镜像集装箱更快搭建集群,在linux上搭建
	**3.安装服务(redis,nginx等)更加简洁**
	软件直接从docker仓库下载不用再一个个下载和配置集成环境了
	**4.使不同服务之间相互隔离(单程序运行出错不会发生雪崩)**
	集装箱概念让运行不会收到牵连
架构图
	![[Docker和Jekins(去冲突和快速打包)_image_1.jpg]]
	docker就是docker软件客户端
	hosts就是我们主机 其中的deamon(虚拟光驱)就是集成在主机中的的docker
	Registries就是docker的集装箱仓库,仓库内包含Image(镜像文件)
	container(容器):镜像和容器的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义(类)，容器是镜像运行时的实体(实例)。容器可以被创建、启动、停止、删除、暂停等。
Docker运行一个项目的流程:
	Docker 使用客户端-服务器 (C/S) 架构模式，使用远程API来管理和创建Docker容器。
	1.安装docker
	2.项目搭建->docker从镜像仓库拉取所需的镜像,在主机上实例化对应的集群->完成
Docker安装(docker-ce社区版)
	**Linux(CentOS7):**
	1.推荐通过yum安装(也可以自己下rpm)
	环境需求:yun install -y yum-utils
	然后通过utils去 yum-config-manager\
	--add-repo \
	https://download.docker.com/linux/centos/docker-ce.repo
	设置docker的镜像仓库地址
	(下载和配置完后可以cd /etc/yum.repos.d文件夹中看docker-ce.erpo文件的仓库地址是否正确)
	下载并安装指定版本的docker
	查看docker的版本
	yum list docker-ce --showduplicates | sort -r
	安装docker
	yum install docker-ce-18.09.0 docker-ce-cli-18.09.0 containerd.io -y
	2.设置开机启动(同mysql的命令,启动则使用start即可)
	systemctl enable docker
	3.配置docker的镜像仓库地址(可到阿里云社区中搜索Docker镜像加速器查找)
	![[Docker和Jekins(去冲突和快速打包)_image_2.jpg]]
	每个人的地址都不同(修改后可cd /etc/docker查看daemon.json文件)
Docker的常用命令
	**镜像相关:**
	docker images (#列出本地所有的docker iamges)
	docker images --help(查看命令参数)
	docker search redis(#搜索镜像(看不到具体版本号需要到下面地址查找))
	镜像查找地址：[https://hub.docker.com/](https://hub.docker.com/)
	![[Docker和Jekins(去冲突和快速打包)_image_3.jpg]]
	docker pull 镜像名:镜像的版本号(#下载指定版本)
	docker rmi 镜像id(#根据镜像id删除，注意如果两个镜像的镜像id（id前缀）一样，是无法被删除的
	docker rmi 镜像:版本号(#根据镜像名:版本删除)
	**容器相关:(docker run --help查看文档)**
	容器实例命令格式： ==docker run \[OPTIONS] IMAGE COMMAND==
	**OPTIONS说明(常用)：** 
	–-name=‘容器新名字’：为容器自定义一个名称
	--rm 运行完容器退出后删除
	-d：(detach)后台运行容器，并返回容器ID，也叫守护式容器 -i：(interactive)以交互模式运行容器(一般配合-t使用可以进去输入linux命令,exit退出后后台也随之退出) -t：(terminal)为容器重新分配一个伪终端
	-P：指定容器端口映射到宿主机端口
	-v (volume)容器绑定数据卷
	docker run -it --name=自定义容器名 软件名:版本号/bin/bash(#根据镜像创建容器实例,并通过交互终端进去容器)
	docker run -d --name 自定义容器名 软件名:版本号
	(后台方式启动软件)
	docker run -d --name 容器名 -p 宿主机端口:容器端口 --rm软件名:版本号(后台方式启动端口映射到宿主机的端口,使用后退出自动删除容器)
	docker exec -it 容器名 /bin/bash(进入指定容器的交互页面)
	docker inspect 容器名(查看容器详细参数)
	docker rm 容器名 (删除指定容器)
	docker stop 容器名 (停止指定容器)
	docker start 容器名 (开始指定容器)
	命令： docker ps [OPTIONS]
	**OPTIONS说明：** 
	1. \-a :列出当前所有正在运行的容器+历史上运行过的 
	2. \-l :显示最近创建的容器。
	3. \-n：显示最近n个创建的容器。 
	4. \-q :静默模式，只显示容器编号。
	5. \–no-trunc :不截断输出。
	docker ps(查看正在运行的容器)
	docker pa -a(查看正在运行的容器和已经退出的)
docker安装具体软件:
	**mysql:(具体文档可以查看dockerHub)**
	docker pull mysql:5.7 ->安装
	docker run -d --name mysql01 -p3307:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
	->创建mysql01的容器设置密码为123456端口号在宿主机上对应3307
	docker exec -it mysql01 /bin/bash->#进入容器内部
	grant all privileges on *.* to root@'%' identified by "123456"; ->设置所有ip使用root用户登录给与所有权限
	flush privileges; ->刷新配置
	**nginx安装同理:**
	![[Docker和Jekins(去冲突和快速打包)_image_4.jpg|500]]
	以第一种映射方式映射到宿主机
	\#创建数据卷
	docker volume create nginx-static
	docker volume create nginx-conf
	\#绑定【映射】
	docker run -d -p 8888:80 --name nginx04 \
	-v nginx-static:/usr/share/nginx \
	-v nginx-conf:/etc/nginx \
	nginx:1.8
	以第二种映射方式将conf配置和html文件夹映射到宿主机对应文件或文件夹
	docker run -d --name nginx03 -v export/server/mynginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /export/server/mynginx/html:/usr/share/nginx/html \
	-p 8081:80 nginx:1.8
	注意:
	1.以第二种映射方式映射宿主机上如果映射开始时没有创建文件夹和文件则映射的时候一律当文件夹识别,所以conf文件如果要映射到文件上则要自行先创建文件和文件夹
	2.conf文件映射完成后并不能正确显示nginx页码,因为conf中的配置的静态资源是宿主机上的资源容器无法访问,应该改成容器中自己的静态资源
	**reids同理:**
	启动redis容器
	docker run -d --name redis01 -p6379:6379 redis:6.0.9 redis-server --appendonly yes
DockerUI(Portainer可视化管理docker)
	Portainer是Docker的图形化管理工具，提供状态显示面板、应用模板快速部署、容器镜像网络数据卷的基本操作（包括上传下载镜像，创建容器等操作）、事件日志显示、容器控制台操作、Swarm集群和服务等集中管理和操作、登录用户管理和控制等功能。功能十分全面，基本能满足中小型单位对容器管理的全部需求。
	\# 查询当前有哪些Portainer镜像
	docker search portainer
	\# 下载镜像
	docker pull portainer/portainer
	\#启动
	docker run -d -p 9000:9000 \
	--restart=always \
	-v /var/run/docker.sock:/var/run/docker.sock \
	--name myprtainer \
	--privileged \
	portainer/portainer
	然后打开 ip地址:9000/查看页面即可
	注册账号密码账号:admin密码:123456123456

---
## Docker的容器中的数据持久化(数据卷)

数据卷
	\#数据卷本质就是宿主机的一个目录 默认存放路径为
	var/lib/docker/volumes/
	![[Docker和Jekins(去冲突和快速打包)_image_5.jpg]]
	1.数据卷就是宿主机(linux主机)的一个文件夹或文件,
	2.容器中的数据或配置与数据卷绑定后可以双向传输
	3.一个数据卷可以被多个容器绑定
	4.一个容器可以绑定多个数据卷
	5.容器删除时数据卷不会删除
数据卷相关命令:
	docker volume create 数据卷名称(#创建数据卷的命令,tomcat为数据卷的名称)
	docker volume inspect 数据卷名(查看数据卷详细信息)
	容器绑定数据卷: -v 命令
	第一种方式:-v(数据卷名:容器文件夹地址)会复制容器中文件
	例:docker run -d -p 宿主机端口号:容器软件端口号 --name 容器名 -v 数据卷名:/usr/local/tomcat(容器中的文件夹映射到宿主机上数据卷) 软件名:版本号
	第二种方式:-v(自定义地址(必须绝对路径):容器文件夹地址)映射到自定义地址作为数据卷,且容器文件夹下内容在初始化时不会被映射,但是后续更新内容还是会映射上

---
## Dockerfile(脚本文档)
DockerFile作用
	是一个文本文档，DockerFile是一个泛称->将项目push到镜像仓库的脚本文件,文件中包含了一条条的指令
	使用DockerFile构建一个自己的全新镜像
	![[Docker和Jekins(去冲突和快速打包)_image_6.jpg|400]]

镜像生成流程:
	1.编写dockerFile
	![[Docker和Jekins(去冲突和快速打包)_image_7.jpg|360]]
	2.构建镜像
	docker build -f dockerfile.txt -t app:1.0 . (别漏了最后的 . )
	3.启动容器
	docker run -it -p 8881:8888 app:1.0 (不要带/bin/bash会覆盖dockerFile中的CMD的初始化启动命令)
	4.将本地的镜像放到镜像仓库(阿里云镜像仓库或dockerhub)
	例:阿里云镜像仓库->实例列表->默认实例个人版->创建命名空间->创建镜像(名字叫创建镜像仓库)->按照阿里云页面的提示流程到linux中登录(密码到访问凭证中拿)
	![[Docker和Jekins(去冲突和快速打包)_image_8.jpg]]
	->tag重命名镜像(规范名称)然后命名版本号->push命令将本地推到镜像仓库
常用指令(关键词):
	FROM 指定当前自定义镜像需要依赖的镜像
	RUN 执行linux命令，比如cd mkdir等
	WORKDIR：声明镜像的默认工作目录，当容器启动后自动跳到该目录
	ADD 将宿主机的jar包加入到自定义的镜像的目录中
	CMD：需要执行的命令，在workdir目录下执行这条命令,容器启动（在workdis目录）执行该命令

---
**CI/CD(持续集成和持续部署)**
主要目的:
	![[Docker和Jekins(去冲突和快速打包)_image_9.jpg]]
	在新代码从开发到部署的过程中(打jar包并测试的重复劳动脚本化)，尽量减少人工的介入。
持续集成(Continuous Integration):
	在和向远程仓库 push 代码后，在这次提交合并入主分支前进行一系列测试，构建等流程。假设现在有个应用的代码存储在 git 上，每天开发者都 push 很多次提交，针对每次 push，你可以创建一系列脚本进行自动测试，降低往应用里引入错误的概率。这就是持续集成。
持续部署(Continuous Deployment):
	在持续集成的基础上更进一步，指将推送指仓库默认分支的部署至产品环境。如果这部分需要手动触发，这就是一个持续交付（Continuous Delivery）环节
	![[Docker和Jekins(去冲突和快速打包)_image_10.jpg]]


## Jekins(docker版)
**主要目的:实现CICD流程简化测试部署环境**
jekins安装:
	1.下载
	docker pull jekins/jekins:lts (下载latest最新版本)
	2.启动jekins容器并绑定数据卷
	启动jekins容器
	docker run -d -p 9988:8080 -p 50000:50000 \
	-v /root/export/server/jdk1.8.0_261/bin/java:/root/export/server/jdk1.8.0_261/bin/java \
	-v /root/export/server/maven:/root/export/server/maven
	-v /root/export/server/jdk1.8.0_261:/root/export/server/jdk1.8.0_261 \
	-v jenkins-data1:/var/jenkins_home \
	-v /etc/localtime:/etc/localtime \
	--name jenkins01 jenkins/jenkins:lts
	3.登录首页进入可视化配置
	输入ip地址:9988/到jekins首页
	docker exec 容器名 tail /var/jenkins_home/secrets/initialAdminPassword
	找到初始密码
	进入新手入门页面->安装推荐的插件来保证我们功能!
	安装完后进入Manage jekins->golbal tool Configuration配置jdk和maven
	注意:jdk和maven的HOME一定是jekins容器中的地址,maven还需要配置setting.conf中maven仓库地址
	4.集成Gitee并将项目发送到gitee
	tool Manage->搜索gitee安装gitee插件后重启
	到gitee中登录账号到设置中选择权限生成私人令牌,复制私人令牌唯一码->到Configure System中配置gitee地址->保存
	在gitee上新建仓库(名字与项目名一致)->复制ssh链接或http链接地址->到idea中准备上传项目到新仓库(注意.idea文件和.iml和target包等不要上传会造成冲突,所以配置忽略脚本上传时默认取出.idea等冲突包不上传)->下载.ignore文件生成模板然后手动写脚本(网上复制)->使用gitcommit后push到远程仓库
	至此第一步完成 -------------------------------------------------------------------------------
	5.在jekins构建项目包(maven)并将gitee上的项目地址填入
	首先到plugins Manager下载最新版的 Maven Instergration项目构建插件(保证jekins创建maven项目)
	然后首页新疆项目选择maven进入后到配置源码管理(source Manage)
	选择git然后填入gitee上的远程仓库地址,然后Credentials把gitee账号密码生成凭证给与jekins
	6.构建触发器Build Triggers(什么时候触发打jar包)
	使用默认的build whenever a snapshot dependenct is bulit
	按钮触发
	7.设置build操作
	Root POM(填入pom.xml表示依赖pom进行build)
	Goals and options填入
	clean install -Dmaven.test.skip=true(clean后忽略测试打成jar包)
	(pre steps(构建前操作)post steps(构建后操作))
	至此第二部完成---------------------------------------------------------------------------------
	8.配置完成后到plugins Manage下载SSH Publishers(Publish Over SSH)实现第三步将jar包运到指定服务器->然后到configuration中配置SSH(passphrase虚拟机密码(linux登录密码),SSH servers虚拟机ip地址和username(linunx的ip地址和登录username))->保存
	然后到项目包source Manage中设置post steps(Run Regardless of build result项目构建完无视失败成功后运出)
	->Send files or execute commands over SSH(使用SSH运出去) \**/target/指定jar包名称 表示要运送的jar包
	指定jar包去除XXX/target前缀
	选择运送到指定目录(linux指定目录)
	然后输入命令(笔记最后,自定义修改)
	保存->应用
	至此第三步完成(jekins帮我们实现了脚本化构建)-----------------------------------------------
	然后到首页点按钮构建即可,出问题看控制台输出debug
Docker私有仓库搭建:
	![[Docker和Jekins(去冲突和快速打包)_image_11.jpg]]
	![[Docker和Jekins(去冲突和快速打包)_image_12.jpg]]

jekins使用SSH运输jar包需要的命令
	\# 必须加，不然jekins会kill 应用程序
	export BUILD_ID=dontKillMe
	\# 应用存放地址（根据实际存储的位置修改路径）
	APP_HOME=/root/export/server/
	\# 应用名称（部署的项目的应用名称）
	APP_NAME=gateway-1.0-SNAPSHOT.jar
	\# 获取PID
	PID=$(ps -ef |grep ${APP_NAME} |grep -v grep |awk '{print $2}')
	if \[ -z "${PID}" ]; then
	echo "进程不存在启动进程"
	cd ${APP_HOME}
	chmod 777 ${APP_NAME}
	source /etc/profile
	\#防止nohup.out文件过大
	cat /dev/null > nohup.out
	\#这个命令就是启动jar工程的命令，同时指定加载的配置文件的类型是 dev
	nohup java -jar ${APP_NAME} --spring.profiles.active=dev &
	if \[ $? = 0 ];then
	sleep 1
	tail -n 50 nohup.out
	fi
	echo "finished!!!"
	else
	echo "进程已经存在杀死进程"
	kill -9 ${PID}
	echo "${APP_NAME} process stop, PID=${PID}"
	cd ${APP_HOME}
	chmod 777 ${APP_NAME}
	source /etc/profile
	\#防止nohup.out文件过大
	cat /dev/null > nohup.out
	nohup java -jar ${APP_NAME} --spring.profiles.active=dev &
	if \[ $? = 0 ];then
	sleep 1
	tail -n 50 nohup.out
	fi
	echo "finished!!!"
	fi