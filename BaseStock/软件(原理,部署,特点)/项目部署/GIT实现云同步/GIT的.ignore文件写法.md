##### 常用:
1. 以”#”号开头表示注释； 
2. 以斜杠“/”开头表示目录； 
3. 以星号“*”通配多个字符；
4. 以问号“?”通配单个字符 
5. 以方括号“[]”包含单个字符的匹配列表； 
6. 以叹号“!”表示不忽略(跟踪)匹配到的文件或目录；

[详情](https://blog.csdn.net/w8y56f/article/details/103263924)






例子:
# Compiled class file  
*.class  
*.classpath  
*.factorypath  
  
# Log file  
*.log  
  
# BlueJ files  
*.ctxt  
  
# Mobile Tools for Java (J2ME)  
.mtj.tmp/  
  
# Package Files #  
*.jar  
*.war  
*.ear  
*.zip  
*.tar.gz  
*.rar  
  
# virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml  
hs_err_pid*  
  
# IDE Files #  
*.iml  
.idea  
.idea/  
.project  
.settings  
target  
.DS_Store  
  
# temp ignore  
*.cache  
*.diff  
*.patch  
*.tmp  
  
# Maven ignore  
.flattened-pom.xml  
  
# AsciiDoc  
spring-cloud-alibaba-docs/**/*.html