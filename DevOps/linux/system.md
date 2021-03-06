# **系统**

## **权限、用户管理**

### **用户、用户组**

用户/组是操作系统身份认证资源。两者通过uid/gid唯一确定，如果存在两个用户uid/gid相同，则这两个用户具有相同权限。通常uid/gid一一对应。

linux中用户分为三种：

- 管理员，root，uid=0。用户名可以不为root，uid也可以，也可以添加另一个管理员用户。
- 系统用户，uid=1-499/201-999，shell=/sbin/nologin。系统用户一般不做登录用途，具有某些特殊权限或特殊用途。
- 普通用户，uid=1000~65534。可以执行有限操作。

用户组类似。

#### **/etc/passwd、/etc/group、/etc/shadow**

这些文件存储了用户及用户组信息。

##### **/etc/passwd**

```shell
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
```

从左至右，以冒号分割：

- 用户名
- 原用户密码，现为x
- uid
- gid
- 用户注释信息
- 用户家目录
- 默认登录shell，可以为脚本

##### **/etc/shadow**

```shell
$ cat /etc/shadow
root:$6$Iiq.3taj$dvBm76EdkRFkVTBGZ5rzcyotLRW9/leLi5bD5i2CtQMak2zqw7gw/Ptf9VmQvnz9YBaFW35URJWU9Z7fuJi3n0:18367:0:99999:7:::
daemon:*:18113:0:99999:7:::
bin:*:18113:0:99999:7:::
sys:*:18113:0:99999:7:::
```

从左至右，以冒号分割：

- 用户名
- 密码
  - 空：用户没有密码
  - *：用户被锁定
  - 以!或!!开头：用户被锁定
  - !：用户被锁定，可以使用ssh key或su方式登入
  - !!：为设定密码
  - \$id\$salt\$hashed：表示加密后的密码。\$id表示使用的加密算法，\$salt表示加密时用的盐，\$hashed表示加密后的密码。
- 上次修改密码时间到19700101的天数，计算方式下同
- 密码最少使用天数，0或空表示不设置
- 密码最长使用天数，0或99999表示永不过期
- 密码过期前提醒天数，0或空表示不提醒
- 密码过期后宽限天数
- 用户过期天数，0表示永不过期
- 保留

##### **/etc/group**

```shell
$ cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,server_1
```

从左至右，以冒号分割：

- 用户组名
- 占位符
- gid
- 该组下的用户，逗号分割

#### **用户、用户组管理命令**

用户通过useradd(adduser为useradd软连接)创建，passwd创建密码，usermod编辑用户信息，userdel删除用户。
用户组通过groupadd创建，group编辑用户组信息。

#### **查看用户相关信息命令**

id、users、last、who、w

### **权限**

文件信息中定义了不同角色的权限信息。
三种角色：

- 属主：u
- 属组：g
- 其他：o

三种权限：

三种权限可以用字母表示，也可以用数字表示。

- r：4
- w：2
- x：1
- -：没有该权限

每个角色都有三种权限的定义，可以通过ls -l查看文件权限信息。每个角色的权限都可以用一个八进制数表示，即用0-7表示每个角色三种权限的和（若某角色有rw权限，则八进制数为6）。
不同文件权限表示的信息不同。
普通文件：

- r：可读
- w：可写
- x：可执行

目录文件：

- r：可以读取目录data block内容（可以ls）
- w：可以创建、删除目录下的文件、目录
- x：可以访问目录（可以cd）

#### **umask**

创建文件时的默认权限，root用户为022，普通用户为002。
在/etc/profile和/etc/bashrc设置。

创建目录时，目录权限为777-创建目录用户的umask
创建文件时，文件权限为666-创建文件用户的umask，若umask某位为奇数，则+1

#### **ACL access control list**

用于解决所有者、所属组和其他这三个权限位无法合理设置单个用户权限的问题。

#### **隐藏属性**

#### **suid/sgid/sbit**

数字表示分别为4/2/1，字母表示有两种，第一种对应位置存在x权限，则表示为s/s/t，第二种对应位置不存在x权限，则表示为S/S/T。

suid：用于可执行文件提权，对可执行文件授予所有者权限
guid：用于二进制文件或目录提权，对其授予所属组权限或继承父目录所属组
sbit：用户目录，设置后使得目录下文件只有文件所有者可以删除

## 磁盘管理



## 文件系统



## **硬件资源管理**

> ??? 为什么要进程分组
> ??? 进程分组的依据

### **CGROUP**

Linux Control Group 简称 cgroup。由内核提供的一种限制单一进程或多个进程使用资源的机制。

#### **概念**

##### **subsystem**

cgroup为每种可控制的资源定义了一个子系统。子系统不仅包含可分配资源的子系统，也包含监控类型的子系统。

##### **hierarchy**

由一些cgroup构成的树状结构层次。层次在创建时会默认创建顶级cgroup（root cgroup），该cgroup中包含系统中所有进程。

##### **规则**

1. 单个层级可以拥有一个或多个子系统。
2. 子系统不能同时附加在两个或以上的层次上。
3. 层次在创建时会默认创建顶级cgroup（root cgroup），该cgroup中包含系统中所有进程。
4. 一个任务（进程）可以位于多个cgroup中，只要每个cgroup处于不同的层次结构中即可。
5. 系统中的进程（任务）创建子进程（任务）时，该子任务自动成为其父进程所在 cgroup 的成员。然后可根据需要将该子任务移动到不同的 cgroup 中，但开始时它总是继承其父任务的 cgroup。

##### 默认层级

系统资源会默认分为四个cgroup：user、system、machine、-。
systemd会默认创建slice、scope、service单位层级。
每个cgroup都是一个slice，可以由子slice。

**默认cgroup**：

- user.slice：用户会话默认位置，每个用户会话都会在该 slice 下面创建一个子 slice，如果同一个用户多次登录该系统，仍然会使用相同的子 slice。
- system.slice：所有系统 service 的默认位置。
- machine.slice：所有虚拟机和 Linux 容器的默认位置。
- -.slice：根slice。

**单位层级类型**：

- slice：一组按照层级排列的单位层级。slice不包含进程，进程包含在service和scope中，slice会组建一个包含service、scope的层级。
- scope：一组外部创建的进程。由任意进程通过fork()函数启动或停止，然后在systemd在运行时注册。用户会话、容器、虚拟机包含在scope中。
- service：一个或一组进程。由systemd依据配置文件启动。


