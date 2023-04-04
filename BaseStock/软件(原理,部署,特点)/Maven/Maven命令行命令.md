#### 常用命令:
1. mvn clean   清除产生的项目
2. mvn install 打包并安装
3. 
4. 编译源代码： mvn compile 
5.  编译测试代码：mvn test-compile    
6.  运行测试：mvn test   
7.  产生 site：mvn site   
8.  打包：mvn package   
9.  在本地 Repository 中安装 jar：mvn install 
10.  
11.  生成 eclipse 项目：mvn eclipse:eclipse  
12.  生成 idea 项目：mvn idea:idea  
13.  组合使用 goal 命令，如只打包不测试：mvn -Dtest package   
14.  编译测试的内容：mvn test-compile  
15.  只打 jar 包: mvn jar:jar  
16.  只测试而不编译，也不测试编译：mvn test -skipping compile -skipping test-compile


[常用命令大全](https://segmentfault.com/a/1190000039379099)
