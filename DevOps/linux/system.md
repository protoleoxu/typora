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

#### umask

创建文件时的默认权限，root用户为022，普通用户为002。
在/etc/profile和/etc/bashrc设置。

创建目录时，目录权限为777-创建目录用户的umask
创建文件时，文件权限为666-创建文件用户的umask，若umask某位为奇数，则+1

#### ACL access control list

用于解决所有者、所属组和其他这三个权限位无法合理设置单个用户权限的问题。





## 磁盘管理

## 文件系统

## 硬件资源管理
