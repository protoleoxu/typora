# basic.md

## linux 基础

### Linux哲学思想

一切皆文件：
     把几乎所有资源统统抽象为文件形式；包括硬件设备，甚至通信接口等；
     open(), read(), write(), close(), delete(), create()
由众多功能单一的程序组成；一个程序只做一件事，并且做好；
     组合小程序完成复杂任务；
程序运行后尽量避免跟用户交互；
     目标：易于以编程的方式实现自动化任务；  
使用文本文件保存配置信息；

### 文件/目录/文件系统

目录：路径映射；
文件：存储空间存储的一段流式数据，对数据可以做到按名存取；
文件系统：层级结构；有索引；
     /: 原初起点；
         倒置树状结构；

         /dev/pts/2:
             最左侧/: 表示根目录
             其它的/: 表示路径分隔符
                 Linux的路径分隔符是/
                 Windows的是\

     文件的路径表示：
         绝对路径：从根开始表示出的路径
         相对路径：从当前位置开始表示出的路径

     文件名使用法则：
         严格区分字符大小写：file1, File1, FILE1
         目录也是文件，在同一路径下，两个文件不能同名；
         支持使用除/以外的任意字符；
         最长不能超过255个字符；

     用户有家目录：home,
         用户的起始目录；普通用户管理文件的位置；
     工作目录：

         /etc/sysconfig/network-scripts/ifcfg-eno16777736
             basename：最右侧的文件或目录名；
             dirname：basename左侧的路径；

         命令：
             ~]# basename /PATH/TO/SOMEFILE
                 SOMEFILE
             ~]# dirname /PATH/TO/SOMEFILE
                 /PATH/TO

### 命令

命令的语法通用格式：
    # COMMAND OPTIONS ARGUMENTS

    COMMAND: 
        发起一命令：请求内核将某个二进制程序运行为一个进程；
            程序 --> 进程
            静态 --> 动态（有生命周期）

        命令本身是一个可执行的程序文件：二进制格式的文件，有可能会调用共享库文件；
            多数系统程序文件都存放在：/bin, /sbin, /usr/bin, /usr/sbin，/usr/local/bin, /usr/local/sbin
                普通命令：/bin, /usr/bin, /usr/local/bin
                管理命令：/sbin, /usr/sbin, /usr/local/sbin
            共享库：/lib, /lib64, /usr/lib, /usr/lib64, /usr/local/lib, /usr/local/lib64
                32bits的库：/lib, /usr/lib, /usr/local/lib
                64bits的库：/lib64, /usr/lib64, /usr/local/lib64

            注意：并非所有的命令都有一个在某目录与之对应的可执行程序文件

        命令必须遵循特定格式规范：exe, msi, ELF(Linux)
            ~]# file /bin/ls

        命令分为两类：
            由shell程序的自带的命令：内置命令(builtin)
            独立的可执行程序文件，文件名即命令名：外部命令

        shell程序是独特的程序，负责解析用户提供的命令；
            环境变量：
                PATH：从哪些路径中查找用户键入的命令字符串所对应的命令文件；
                    ~]# echo $PATH
                    /usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin

                    查找次序：自左而右；

        查看命令类型：
            type COMMAND

    OPTIONS：
        指定命令的运行特性；

        选项有两种表现形式：
            短选项：-C, 例如-l, -d
                注意：有些命令的选项没有-；
                如果同一命令同时使用多个短选项，多数可合并：-l -d = -ld
            长选项：--word, 例如--help, --human-readable
                注意：长选项不能合并；

            注意：有些选项可以带参数，此称为选项参数；

    ARGUMENTS：
        命令的作用对象；命令对什么生效；

        注意：不同的命令的参数；有些命令可同时带多个参数，多个之间以空白字符分隔；

    例如：ls -ld /var /etc 

获取命令的使用帮助：
    内部命令：
        help COMMAND
    外部命令：
        (1) 命令自带简要格式的使用帮助
            # COMMAND --help
        (2) 使用手册：manual
            位置：/usr/share/man
            # man COMMAND

                SECTION：
                    NAME：功能性说明
                    SYNOPSIS：语法格式
                    DESCRIPTION：描述
                    OPTIONS：选项
                    EXAMPLES：使用示例
                    AUTHOR: 作者
                    BUGS: 报告程序bug的方式
                    SEE ALSO: 参考
                    ...

                SYNOPSIS: 
                    []：可选内容；
                    <>：必须提供的内容；
                    a|b|c：多选一；
                    ...：同类内容可出现多个；

            使用手册：压缩格式的文件，有章节之分；
                /usr/share/man
                    man1, man2, ...

                    1：用户命令；
                    2：系统调用；
                    3：C库调用；
                    4：设备文件及特殊文件；
                    5：文件格式；（配置文件格式）
                    6：游戏使用帮助；
                    7：杂项；
                    8：管理工具及守护进行；

                ~]# man CHAPTER COMMAND

                    注意：并非每个COMMAND在所有章节下都有手册；
                        查看：
                            ~]# whatis COMMAND

                            注意：其执行过程是查询数据库进行的；
                                手动更新数据库：
                                    ~]# makewhatis

            man命令打开手册以后的操作方法：
                翻屏：
                    空格键：向文件尾翻一屏；
                    b: 向文件首部翻一屏；
                    Ctrl+d：向文件尾部翻半屏；
                    Ctrl+u：向文件首部翻半屏；
                    回车键：向文件尾部翻一行；
                    k: 向文件首部翻一行；
                    G：跳转至最后一行；
                    #G: 跳转至指定行；
                    1G：跳转至文件首部；

                文本搜索：
                    /keyword：从文件首部向文件尾部依次查找；不区分字符大小写；
                    ?keyword：从文件尾部向文件首部依次查找； 
                        n: 与查找命令方向相同；
                        N: 与查找命令方向相反；

                退出：
                    q: quit

            选项：
                -M /PATH/TO/SOMEDIR：到指定目录下查找命令手册并打开之；

            练习：获取useradd命令的用法
                (1) 添加用户gentoo；
                    # useradd gentoo
                    # id gentoo
                (2) 添加用户slackware，要求指定其所用的shell为/bin/tcsh；
                    # useradd -s /bin/tcsh slackware
                    # tail -1 /etc/passwd

        (3) info COMMAND
            获取命令的在线文档；

        (4) 很多应用程序会自带帮助文档：/usr/share/doc/APP-VERSION
            README：程序的相关的信息；
            INSTALL: 安装帮助；
            CHANGES：版本迭代时的改动信息；

        (5) 主流发行版官方文档
            http://www.redhat.com/doc

        (6) 程序官方的文档：
            官方站点上的“Document”

        (7) 搜索引擎
            google

                keyword filetype:pdf
                keyword site:domain.tld
                ...

    书籍的出版社：
        O'Reiley
        Wrox
        机械工业、电子工业、人邮、清华大学、水利水电

常用命令：

    Linux文件系统：
        1、文件名名称严格区分字符大小写；
        2、文件可以使用除/以外任意字符；
        3、文件名长度不能超过255字符；
        4、以.开头的文件为隐藏文件；
            .: 当前目录；
            ..: 当前目录的上一级目录；
                /etc/sysconfig/
                    .: sysconfig
                    ..: /etc

    工作目录：working directory
    家目录：home

    常用命令：
        pwd: printing working directory
            显示工作目录

        cd：change directory
            cd [/PATH/TO/SOMEDIR]
                cd: 切换回家目录；
                    注意：bash中, ~表示家目录；
                cd ~：切换回自己的家目录
                cd ~USERNAME：切换至指定用户的家目录；
                cd -：在上一次所在目录与当前目录之间来回切换；
                    相关的环境变量
                        $PWD：当前工作目录
                        $OLDPWD：上一次的工作目录

                .
                ..

        ls: list, 列出指定目录下的内容
            ls [OPTION]... [FILE]...

                -a: 显示所有文件，包括隐藏文件；
                -A：显示除.和..之外的所有文件；
                -l: --long, 长格式列表，即显示文件的详细属性信息；
                    -rw-r--r--. 1 root   root     8957 10月 14 19:34 boot.log
                    -：文件类型，-, d, b, c, l, s, p
                    rw-r--r--
                        rw-：文件属主的权限；
                        r--：文件属组的权限；
                        r--：其它用户（非属主、属组）的权限；
                    1：数字表示文件被硬链接的次数；
                    root：文件的属主；
                    root：文件的属组；
                    8957：数字表示文件的大小，单位是字节；
                    10月 14 19:34 ：文件最近一次被修改的时间；
                    boot.log：文件名
                -h, --human-readable：对文件大小单位换算；换算后结果可能会非精确值；
                -d：查看目录自身而非其内部的文件列表；
                -r: reverse, 逆序显示；
                -R: recursive，递归显示；

        cat：concatenate
            文件文本查看工具；
                cat /etc/fstab
                cat /etc/passwd

            cat [OPTION]... [FILE]...
                -n：给显示的文本行编号；
                -E: 显示行结束符$；

        tac：
            文件文本查看工具；
                tac /etc/fstab
                tac /etc/passwd

            tac [OPTION]... [FILE]...
                -n：给显示的文本行编号；
                -E: 显示行结束符$；

        file：查看文件内容类型；
            file [FILE]...

        echo：回显
            echo [SHORT-OPTION]... [STRING]...
                -n: 不进行换行；
                -e：让转义符生效；
                    \n：换行
                    \t：制表符

                STRING可以使用引号，单引号和双引号均可用；
                    单引号：强引用，变量引用不执行替换；
                        ~]# echo '$SHELL'
                    双引号：弱引用，变量引用会被替换；
                        ~]# echo "$SHELL"

            注意：变量引用的正规符号
                ${name}

        关机或重启命令：shutdown
            shutdown [OPTIONS...] [TIME] [WALL...]
                OPTIONS:
                    -h: halt
                    -r：reboot
                    -c：cancel

                TIME：
                    now
                    hh:mm
                    +m
                        +0

                WALL

        日期相关的命令：
            Linux：系统启动时从硬件读取日期和时间信息；读取完成以后，就不再与硬件相关联；
                系统时钟
                硬件时钟
            
            date：系统时钟
                显示日期时间：date [OPTION]... [+FORMAT]
                    FORMAT：格式符
                        %F
                        %T
                        %Y
                        %m
                        %d
                        %H
                        %M
                        %S
                        %s: 从1970年1月1号(unix元年)0点0分0秒到命令执行那一刻经过的秒数；
                        ...

                设定日期时间：date [MMDDhhmm[[CC]YY][.ss]]

            hwclock, clock：硬件时钟
                显示或设定硬件时钟
                    -s, --hctosys：以硬件为准，把系统调整为与硬件时间相同；
                    -w, --systohc：以系统为准，把硬件时间调整为与系统时钟相同；

            cal：日历
                    cal [[month] year]
