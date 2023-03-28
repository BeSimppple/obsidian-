Vue

为什么使用Vue?

1.操作简单,渲染方便,简化DOM操作

2.类似MVC的mvvm框架,把Model负责数据，View负责显示

ViewModel中转

搭配 elementUI更加强

Vue的常用指令:

1.v-text 2.v-html 3.v-if 4.v-for 5.v-show 6.v-model

7.v-on 8.v-bind 9

Vue的生命周期
![[VUE(脚手架)_image_1.png]]
access-control-allow-origin 跨域拦截,浏览器特有为了安全做的限制,不是服务端的限制,底层可以通过JsonP的原理去解决

解决方法:@CrossOrigin在Controller层,

Vue请求拦截器和响应拦截器

axiod.interceptors.request.use(function(config){

//内容 常用于拦截请求,在请求头中增加内容

})

axiod.interceptors.response.use(function(config){

//内容 常用于拦截响应,转化响应的数据内容

})

Vue常用插件:

1.vue-devtools插件

2.open in browser

3.Live Server

Vue组件:

什么是组件:

- 模块化： 是从代码逻辑的角度进行划分的；方便代码分层开发，保证每个功能模块的职能单一；

- 组件化： 是从UI界面的角度进行划分的；前端的组件化，方便UI组件的重用；

创建组件的3中方式

1.Vue.extend+Vue.component

2.直接使用Vue.componet

3.将template抽离出来(重点使用)(组件标签必须是template,且只能有一个根标签)

因为vm的实例对象为{},所以组件的data必须是function(){return{}}

webpack

NodeJS(webpack的运行环境)

为什么使用webpack?

1.解决传统前端 代码乱 , js之间依赖关系强

2.大量的二次请求

webpack是前端的一个项目构建工具,是基于Node.js开发的前段共聚

webpack并不打包index.html文件,都是从main.js打包

生成bundel。js文件在dist文件夾

使用webpack后:

vue组件和axios组件都只需要通过npm导入

-D 和 --save的差别:

-D只有在编译的时候使用,而save则是编译和运行的时候都使用

npm run dev的快捷按钮,NPM SRCRIPTS还没研究好

  

......VUE--router模式和普通VUE区别

只有一个VUE实例,其他的可以认定为主键VUE,其DATA形式为data(){}

就是单页,只是在单页里面变换

请求类型为options是预发请求

跨域沒法綁定session的JessionID

图片文件的上传

//1.文件传入进来,使用字节格式接收然后传入到七牛云

//2.七牛云返回一个文件地址,,文件地址配合七牛云给的域名地址就是访问链接

//3.将这个拼接的访问链接 和文件名存入到数据库,从数据库提取就可以直接使用访问链接访问文件

注意事项:vue中大写影响很大,很多大写并不支持,文件夹最好也不要大写,容易报错不被识别

NPM

npm是Node.js的包管理工具（package manager）。

为啥我们需要一个包管理工具呢？因为我们在Node.js上开发时，会用到很多别人写的JavaScript代码。如果我们要使用别人写的某个包，每次都根据名称搜索一下官方网站，下载代码，解压，再使用，非常繁琐。

于是一个集中管理的工具应运而生：大家都把自己开发的模块打包后放到npm官网上，如果要使用，直接通过npm安装就可以直接用，不用管代码存在哪，应该从哪下载。

  

npm下载会很慢，因为npm默认从国外下载资源，建议修改

#设置npm镜像源地址

npm config set registry http://registry.npm.taobao.org/

#查看npm镜像下载地址

npm get registry

Vue路由(Route)

1.安装 npm i vue-router --save

2.导入3.创建路由表 4.挂载路由

路由的作用:(单页工程,管理切换页面)
![[VUE(脚手架)_image_2.jpg]]
Vue异步(Axios)

1.安装npm install --save axios vue-axios

2.页面导入

vue脚手架:

为什么使用脚手架?,脚手架的作用?

1.实现组件化开发,代码复用(类似于java中对象类)

2.

  

vue-admin-template下载地址 ：[https://github.com/PanJiaChen/vue-admin-template](https://github.com/PanJiaChen/vue-admin-template)

vue-admin-template学习地址 :[https://panjiachen.gitee.io/vue-element-admin-site/zh/guide/#](https://panjiachen.gitee.io/vue-element-admin-site/zh/guide/#)功能

# 克隆项目

git clone https://github.com/PanJiaChen/vue-admin-template.git

# 进入项目目录

cd vue-admin-template

# 安装依赖

npm install --registry=https://registry.npm.taobao.org

# 本地开发 启动项目

npm run dev

各个文件作用:
![[VUE(脚手架)_image_3.jpg]]
env.development中

# base api

VUE_APP_BASE_API = http://localhost:8040/ #配置基础访问端口号地址

router包下index.js可以配置页面的跳转路径和名称

utils包下request可以配置请求拦截器和响应拦截器

permission.js可以配置请求头(例如token)的获取

![](C:\Users\YuLi\AppData\Local\YNote\data\qq3AE958F571E4572DE9CC711939D04018\1e4cf4e24b5349048c58e3f846a336a5\f15b7ccbf5bc4ea99b51e1d8367cb4e5.jpg)

构建部署(nginx):

1.构建

![](C:\Users\YuLi\AppData\Local\YNote\data\qq3AE958F571E4572DE9CC711939D04018\68f0c2a5c66146c88c1be6bd73c16da0\2ae2938bb4b54b4cb54819ab597d90ed.jpg)

2.复制工程根目录的dist到nginx目录(nginx-1.18.0)下

3.配置nginx.conf

![](C:\Users\YuLi\AppData\Local\YNote\data\qq3AE958F571E4572DE9CC711939D04018\7084b1c65b1d4161918fe92052424257\5a5f28eab2ed47e5b4a707c9ca3fd584.jpg)

注意事项:

如果发生回调回正确内容却报error错误可能是因为 request.js 中 response回调需要判断status code== 20000不可能实现.因此需要删除并修改为     const res = response.data

     return res;//此处代码要修改,默认需要status code为20000 就能正常登录