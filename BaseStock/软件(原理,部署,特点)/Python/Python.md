Python

类似一种软件,有自己的语言模式,可以运行在linux或windows等
3. 使用pip管理Python包

pip install <包名> 安装指定的包

pip uninstall <包名> 删除指定的包

pip list 显示已经安装的包

pip freeze 显示已经安装的包，并且以指定的格式显示

4.修改pip下载源

运行pip install 命令会从网站上下载指定的python包，默认是从 https://files.pythonhosted.org/ 网站上下 载。这是个国外的网站，遇到网络情况不好的时候，可能会下载失败，我们可以通过命令，修改pip现在软件时的 源。

  

格式: pip install 包名 -i 国内源地址

  

示例:

  

pip install ipython -i https://pypi.mirrors.ustc.edu.cn/simple/ 就是从中国科技大学(ustc)的服务器 上下载requests(基于python的第三方web框架)

国内常用的pip下载源列表:

  

阿里云 http://mirrors.aliyun.com/pypi/simple/

中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/

豆瓣(douban) http://pypi.douban.com/simple/

清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/

中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/