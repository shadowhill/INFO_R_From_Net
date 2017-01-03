Copy from :

http://blog.fens.me/r-file-folder/

前言

R语言作为脚本语言，有一套文件系统管理的功能函数，也可以实现

如Python一样的系统管理功能。

本文将详细介绍，R语言的文件系统管理。试试有什么不样吧？

目录

文件系统介绍
目录操作
文件操作
几个特殊的目录
1. 文件系统介绍

计算机的文件系统是一种存储和组织计算机数据的方法，它使得对其

访问和查找变得容易，文件系统使用文件和树形目录的抽象逻辑概念

代替了硬盘和光盘等物理设备使用数据块的概念，用户使用文件系统

来保存数据不必关心数据实际保存在硬盘（或者光盘）的地址为多少

的数据块上，只需要记住这个文件的所属目录和文件名。在写入新数

据之前，用户不必关心硬盘上的那个块地址没有被使用，硬盘上的存

储空间管理（分配和释放）功能由文件系统自动完成，用户只需要记

住数据被写入到了哪个文件中。

R语言和其他编程语言一样，都有对文件系统的操作，包括文件操作

和目录操作，函数API都定义在base包中。

2. 目录操作

系统环境：

Linux: Ubuntu Server 12.04.2 LTS 64bit
R: 3.0.1 x86_64-pc-linux-gnu
2.1 查看目录

查看当前目录下的子目录。


# 启动R程序
~ R

# 当前的目录
> getwd()
[1] "/home/conan/R/fs"

# 查看当前目录的子目录
> list.dirs()
[1] "."     "./tmp"
查看当前目录的子目录和文件。


> dir()
[1] "readme.txt" "tmp"

# 查看指定目录的子目录和文件。
> dir(path="/home/conan/R")
 [1] "A.txt"                             "caTools"
 [3] "chinaWeather"                      "DemoRJava"
 [5] "env"                               "FastRWeb"
 [7] "font"                              "fs"
 [9] "github"                            "lineprof"
[11] "pryr"                              "readme.txt"
[13] "RMySQL"                            "RServe"
[15] "rstudio-server-0.97.551-amd64.deb" "websockets"
[17] "x86_64-pc-linux-gnu-library"

# 只列出以字母R开头的子目录或文件
> dir(path="/home/conan/R",pattern='^R')
[1] "RMySQL" "RServe"

# 列出目录下所有的目录和文件，包括隐藏文件，如 .A.txt
> dir(path="/home/conan/R",all.files=TRUE)
 [1] "."                                 ".."
 [3] ".A.txt"                            "A.txt"
 [5] "caTools"                           "chinaWeather"
 [7] "DemoRJava"                         "env"
 [9] "FastRWeb"                          "font"
[11] "fs"                                "github"
[13] "lineprof"                          "pryr"
[15] "readme.txt"                        "RMySQL"
[17] "RServe"                            "rstudio-server-

0.97.551-amd64.deb"
[19] "websockets"                        "x86_64-pc-linux-

gnu-library"
查看当前目录的子目录和文件，同dir()函数。


> list.files()
[1] "readme.txt" "tmp"

> list.files(".",all.files=TRUE)
[1] "."          ".."         "readme.txt" "tmp"
查看完整的目录信息。


# 查看当前目录权限
> file.info(".")
  size isdir mode               mtime               ctime    

           atime  uid  gid uname grname
. 4096  TRUE  775 2013-11-14 08:40:46 2013-11-14 08:40:46 

2013-11-14 08:41:57 1000 1000 conan  conan

# 查看指定目录权限
> file.info("./tmp")
      size isdir mode               mtime               

ctime               atime  uid  gid uname grname
./tmp 4096  TRUE  775 2013-11-14 14:35:56 2013-11-14 

14:35:56 2013-11-14 14:35:56 1000 1000 conan  conan
2.2 创建目录


# 在当前目录下，新建一个目录
> dir.create("create")
> list.dirs()
[1] "."        "./create" "./tmp"
创建一个3级子目录./a1/b2/c3


# 直接创建，出错
> dir.create(path="a1/b2/c3")
Warning message:
In dir.create(path = "a1/b2/c3") :
  cannot create dir 'a1/b2/c3', reason 'No such file or 

directory'

# 递归创建，成功
> dir.create(path="a1/b2/c3",recursive = TRUE)
> list.dirs()
[1] "."          "./a1"       "./a1/b2"    "./a1/b2/c3" 

"./create"  "./tmp"

# 通过系统命令查看目录结构
> system("tree")
.
├── a1
│   └── b2
│       └── c3
├── create
├── readme.txt
└── tmp
2.3 检查目录是否存在


# 目录存在
> file.exists(".")
[1] TRUE
> file.exists("./a1/b2")
[1] TRUE

# 目录不存在
> file.exists("./aa")
[1] FALSE
2.4 检查目录的权限

检查目录的权限


> df<-dir(full.names = TRUE)

# 检查文件或目录是否存在，mode=0
> file.access(df, 0) == 0
./a1     ./create ./readme.txt        ./tmp
TRUE         TRUE         TRUE         TRUE

# 检查文件或目录是否可执行，mode=1，目录为可以执行
> file.access(df, 1) == 0
./a1     ./create ./readme.txt        ./tmp
TRUE         TRUE        FALSE         TRUE

# 检查文件或目录是否可写，mode=2
> file.access(df, 2) == 0
./a1     ./create ./readme.txt        ./tmp
TRUE         TRUE         TRUE         TRUE

# 检查文件或目录是否可读，mode=4
> file.access(df, 4) == 0
./a1     ./create ./readme.txt        ./tmp
TRUE         TRUE         TRUE         TRUE
修改目录权限。


# 修改目录权限，所有用户只读
> Sys.chmod("./create", mode = "0555", use_umask = TRUE)

# 查看目录完整信息，mode=555
>  file.info("./create")
         size isdir mode               mtime               

ctime               atime  uid  gid uname grname
./create 4096  TRUE  555 2013-11-14 08:36:28 2013-11-14 

09:07:05 2013-11-14 08:36:39 1000 1000 conan  conan

# create目录不可以写
> file.access(df, 2) == 0
./a1     ./create ./readme.txt        ./tmp
TRUE        FALSE         TRUE         TRUE
2.5 对目录重名


# 对tmp目录重命名
> file.rename("tmp", "tmp2")
[1] TRUE

# 查看目录
> dir()
[1] "a1"         "create"     "readme.txt" "tmp2"
2.6 删除目录


# 删除tmp2目录
> unlink("tmp2", recursive = TRUE)

# 查看目录
> dir()
[1] "a1"         "create"     "readme.txt"
2.7 其他功能函数

拼接目录字符串


# 拼接目录字符串
> file.path("p1","p2","p3")
[1] "p1/p2/p3"

> dir(file.path("a1","b2"))
[1] "c3"
获取最底层的子目录名


# 当前目录
> getwd()
[1] "/home/conan/R/fs"

# 最底层子目录
> dirname("/home/conan/R/fs/readme.txt")
[1] "/home/conan/R/fs"

# 最底层子目录或文件名
> basename(getwd())
[1] "fs"
>  basename("/home/conan/R/fs/readme.txt")
[1] "readme.txt"
转换文件扩展路径


# 转换~为用户目录
> path.expand("~/foo")
[1] "/home/conan/foo"
标准化路径，用来转换win或linux的路径分隔符


# linux
> normalizePath(c(R.home(), tempdir()))
[1] "/usr/lib/R"      "/tmp/RtmpqNyjPD"

# win
> normalizePath(c(R.home(), tempdir()))
[1] "C:\\Program Files\\R\\R-3.0.1"
[2] "C:\\Users\\Administrator\\AppData\\Local\\Temp\

\RtmpMtSnci"
短路径，缩减路径的显示长度，只在win中运行。


# win
> shortPathName(c(R.home(), tempdir()))
[1] "C:\\PROGRA~1\\R\\R-30~1.1"
[2] "C:\\Users\\ADMINI~1\\AppData\\Local\\Temp\\RTMPMT~1"
3. 文件操作

3.1 查看文件


> dir()
[1]  "create"  "readme.txt"

# 检查文件是否存在
> file.exists("readme.txt")
[1] TRUE

# 文件不存在
> file.exists("readme.txt222")
[1] FALSE

# 查看文件完整信息
> file.info("readme.txt")
           size isdir mode               mtime               

ctime               atime  uid  gid uname grname
readme.txt    7 FALSE  664 2013-11-14 08:24:50 2013-11-14 

08:24:50 2013-11-14 08:24:50 1000 1000 conan  conan

# 查看文件访问权限，存在
>  file.access("readme.txt",0)
readme.txt
         0
# 不可执行
>  file.access("readme.txt",1)
readme.txt
        -1
# 可写
>  file.access("readme.txt",2)
readme.txt
         0
# 可读
>  file.access("readme.txt",4)
readme.txt
         0

# 查看一个不存在的文件访问权限，不存在
> file.access("readme.txt222")
readme.txt222
           -1
判断是文件还是目录。


# 判断是否是目录
> file_test("-d", "readme.txt")
[1] FALSE
> file_test("-d", "create")
[1] TRUE

# 判断是否是文件
> file_test("-f", "readme.txt")
[1] TRUE
> file_test("-f", "create")
[1] FALSE
3.2 创建文件


# 创建一个空文件 A.txt
> file.create("A.txt")
[1] TRUE

# 创建一个有内容的文件 B.txt
> cat("file B\n", file = "B.txt")
> dir()
[1] "A.txt"      "B.txt"      "create"     "readme.txt"

# 打印A.txt
> readLines("A.txt")
character(0)

# 打印B.txt
> readLines("B.txt")
[1] "file B"
把文件B.txt的内容，合并到 A.txt。


# 合并文件
> file.append("A.txt", rep("B.txt", 10))
 [1] TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE TRUE

# 查看文件内容
> readLines("A.txt")
 [1] "file B" "file B" "file B" "file B" "file B" "file B" 

"file B" "file B" "file B" "file B"
把文件A.txt复制到文件C.txt


# 复制文件
> file.copy("A.txt", "C.txt")
[1] TRUE

# 查看文件内容
> readLines("C.txt")
 [1] "file B" "file B" "file B" "file B" "file B" "file B" 

"file B" "file B" "file B" "file B"
3.3 修改文件权限


# 修改文件权限，创建者可读可写可执行，其他人无权限
> Sys.chmod("A.txt", mode = "0700", use_umask = TRUE)

# 查看文件信息
> file.info("A.txt")
      size isdir mode               mtime               

ctime               atime  uid  gid uname grname
A.txt   70 FALSE  700 2013-11-14 12:55:18 2013-11-14 

12:57:39 2013-11-14 12:55:26 1000 1000 conan  conan
3.4 文件重命名


# 给文件A.txt重命名为AA.txt
> file.rename("A.txt","AA.txt")
[1] TRUE
> dir()
[1] "AA.txt"     "B.txt"      "create"     "C.txt"      

"readme.txt"
3.5 硬连接和软连接

硬连接，指通过索引节点来进行连接。在Linux的文件系统中，保存

在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引

节点号(Inode Index)。在Linux中，多个文件名指向同一索引节点是

存在的。一般这种连接就是硬连接。硬连接的作用是允许一个文件拥

有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止

“误删”的功能。其原因如上所述，因为对应该目录的索引节点有一个

以上的连接。只删除一个连接并不影响索引节点本身和其它的连接，

只有当最后一个连接被删除后，文件的数据块及目录的连接才会被释

放。也就是说，文件真正删除的条件是与之相关的所有硬连接文件均

被删除。
软连接，也叫符号连接（Symbolic Link）。软链接文件有类似于

Windows的快捷方式。它实际上是一个特殊的文件。在符号连接中，

文件实际上是一个文本文件，其中包含的有另一文件的位置信息。
硬连接和软连接，只在Linux系统中使用。


# 硬连接
> file.link("readme.txt", "hard_link.txt")
[1] TRUE

# 软连接
> file.symlink("readme.txt", "soft_link.txt")
[1] TRUE

# 查看文件目录
> system("ls -l")
-rwx------ 1 conan conan   70 Nov 14 12:55 AA.txt
-rw-rw-r-- 1 conan conan    7 Nov 14 12:51 B.txt
dr-xr-xr-x 2 conan conan 4096 Nov 14 08:36 create
-rw-rw-r-- 1 conan conan   70 Nov 14 12:56 C.txt
-rw-rw-r-- 2 conan conan    7 Nov 14 08:24 hard_link.txt
-rw-rw-r-- 2 conan conan    7 Nov 14 08:24 readme.txt
lrwxrwxrwx 1 conan conan   10 Nov 14 13:11 soft_link.txt -> 

readme.txt
文件hard_link.txt是文件readme.txt硬连接文件，文件

soft_link.txt是文件readme.txt软连接文件，

3.5 删除文件

有两个函数可以使用file.remove和unlink，其中unlink函数使用同

删除目录操作是一样的。


# 删除文件
> file.remove("A.txt", "B.txt", "C.txt")
[1] FALSE  TRUE  TRUE

# 删除文件
> unlink("readme.txt")

# 查看目录文件
> system("ls -l")
total 12
-rwx------ 1 conan conan   70 Nov 14 12:55 AA.txt
dr-xr-xr-x 2 conan conan 4096 Nov 14 08:36 create
-rw-rw-r-- 1 conan conan    7 Nov 14 08:24 hard_link.txt
lrwxrwxrwx 1 conan conan   10 Nov 14 13:11 soft_link.txt -> 

readme.txt

# 打印硬连接文件
> readLines("hard_link.txt")
[1] "file A"

# 打印软连接文件，soft_link.txt，由于原文件被删除，有错误
> readLines("soft_link.txt")
Error in file(con, "r") : cannot open the connection
In addition: Warning message:
In file(con, "r") :
  cannot open file 'soft_link.txt': No such file or 

directory
4. 几个特殊的目录

R.home() 查看R软件的相关目录
.Library 查看R核心包的目录
.Library.site 查看R核心包的目录和root用户安装包目录
.libPaths() 查看R所有包的存放目录
system.file() 查看指定包所在的目录
4.1 R.home() 查看R软件的相关目录


# 打印R软件安装目录
> R.home()
[1] "/usr/lib/R"

# 打印R软件bin的目录
> R.home(component="bin")
[1] "/usr/lib/R/bin"

# 打印R软件文件的目录
>  R.home(component="doc")
[1] "/usr/share/R/doc"
通过系统命令，找到R文件的位置。


# 检查系统中R文件的位置
~ whereis R
R: /usr/bin/R /etc/R /usr/lib/R /usr/bin/X11/R 

/usr/local/lib/R /usr/share/R /usr/share/man/man1/R.1.gz

# 打印环境变量R_HOME
~ echo $R_HOME
/usr/lib/R
通过R.home()函数，我们可以很容易的定位R软件的目录。

4.2 R软件的包目录


# 打印核心包的目录
> .Library
[1] "/usr/lib/R/library"

# 打印核心包的目录和root用户安装包目录
> .Library.site
[1] "/usr/local/lib/R/site-library" "/usr/lib/R/site-

library"
[3] "/usr/lib/R/library"

# 打印所有包的存放目录
> .libPaths()
[1] "/home/conan/R/x86_64-pc-linux-gnu-library/3.0"
[2] "/usr/local/lib/R/site-library"
[3] "/usr/lib/R/site-library"
[4] "/usr/lib/R/library"
4.3 查看指定包所在的目录


# base包的存放目录
> system.file()
[1] "/usr/lib/R/library/base"

# pryr包的存放目录
>  system.file(package = "pryr")
[1] "/home/conan/R/x86_64-pc-linux-gnu-library/3.0/pryr"
其实，用R语言操作文件系统还是很方便的。但对于函数命名确实不

太规范，需要我们花时间记忆。
