# Git 过滤文件

## [#](https://funtl.com/zh/supplement2/Git-过滤文件.html#gitattributes).gitattributes

```text
# Windows-specific files that require CRLF:
*.bat       eol=crlf
*.txt       eol=crlf

# Unix-specific files that require LF:
*.java      eol=lf
*.sh        eol=lf
```

1
2
3
4
5
6
7

## [#](https://funtl.com/zh/supplement2/Git-过滤文件.html#gitignore).gitignore

```text
target/
!.mvn/wrapper/maven-wrapper.jar

## STS ##
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans

## IntelliJ IDEA ##
.idea
*.iws
*.iml
*.ipr

## JRebel ##
rebel.xml

## MAC ##
.DS_Store

## Other ##
logs/
temp/
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26

上次更新: 2018-12-31 18:51:48

← [Fiddler 手机抓包](https://funtl.com/zh/supplement2/Fiddler-手机抓包.html)[VMware 安装 Android ](https://funtl.com/zh/supplement2/VMware-安装-Android.html)→