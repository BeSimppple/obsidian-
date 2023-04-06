
---
sorting-spec: |
  target-folder: /*
  /folders
   < a-z
  /files
   < a-z
  IT
  软件(原理,部署,特点)
  网络工程
  常用语法和算法
  学习
  obsidian相关
  target-folder: /IT/Spring/*
  Spring原理
  AOP(基于面动态代理)
  target-folder: /IT/SpringCloud/*
  SpringCloud框架思想
  Nacos(服务注册与发现,配置)
  Sentinel(降级熔断)
  
---

/folders表示文件夹
/files表示文件
< modifed 表示按照修改日期正序排序
< createred 按照创建时间正序排序

[排序插件使用详情](https://github.com/SebastianMC/obsidian-custom-sort/blob/master/advanced-README.md#location-of-sorting-specification-yaml-entry)
[实际使用效果](https://zhuanlan.zhihu.com/p/586842280?utm_id=0)

注意同idea中yaml语法使用不要用tab代替2个空格 关键词后接一个空格,如果是子类关系下行缩进一个空格
下载完插件创建sortspec.md到根目录用于识别然后按照yaml格式编写即可使用
等同于idea中导入新的jar包配置默认地址的yaml文件配置对应参数提供读取

小的会重写大的









**还可以改进的点->**
1. 支持改名同步和链接
2. 直接多个文件夹之间做分割线
3. 文件夹加小图标
类似如下
![[sortspec_image_1.png|200]]