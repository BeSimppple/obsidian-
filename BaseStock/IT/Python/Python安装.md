Windows
	1. 官网下载3.10版Python
	2. 自定义安装路径并勾选自动加入环境变量(如果没有加入则手动到环境变量path中加入python文件夹地址以及python/script文件夹地址)
	3. 勾选pip指令包安装(默认勾选)

Python本地文件夹作用
	**Script**: 存放安装的插件

PIP指令包
	**作用**:类似Linux的yum提供包的下载安装和卸载
	**命令**:
	1. pip install 包名
	2. pip uninstall 包名
	3. pip list 
	4. pip freeze     (显示已经安装的包并以指定格式显示)
	**PIP下载镜像地址修改**
	阿里云 http://mirrors.aliyun.com/pypi/simple/
	中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
	豆瓣(douban) http://pypi.douban.com/simple/
	清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
	中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/
	**命令方式**:pip install -i https://pypi.tuna.tsinghua.edu.cn/simple gevent
	**配置文件修改:**
	linux
		修改 ~/.pip/pip.conf (没有就创建一个)
	windows
		使用命令pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
		1.python会自动创建一个pip.ini文件并返回地址给你例如:C:\\Users\\用户名\\AppData\\Roaming\\pip.ini
	**例子:**
	[global]
	timeout = 6000
	index-url = https://mirrors.aliyun.com/pypi/simple/
	trusted-host = mirrors.aliyun.com
安装集成开发环境pycharm(或直接使用idea加python插件)
	Idea和PyCharm的区别就是Idea需要安装Python的插件，而PyCharm是专门用来开发Python的工具，Idea在安装插件后基本没啥区别，唯一点就是可能官方的插件没有PyCharm更新得快
	**操作**:
	1. Idea中安装python插件并重启
	2. 新建项目->如果有python在左侧栏则选择,如果没有则新建项目中找到python语言并选择即可
IDEA中代码提示/改错
	






