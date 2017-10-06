# 常用Linux命令

## 1、查看当前在线用户命令 w vs who

1.1 当我们使用w命令查看当前用户时，系统默认输出如下内容：

```shell
23:52:23 up 20:31,  2 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     tty1                      日14    2days  0.00s  0.00s -bash
root     pts/0    10.211.55.2      一22    7.00s  0.37s  0.00s w
```

其中，第一行中会分别显示当前时间，登录的用户数，以及使用该命令之前的1分钟、5分钟以及15分钟之内的系统负载率。第二行中类似表格的表头，指出每一列数据指代的内容：USER表示该用户的userid；TTY表示用户登陆的终端设备及其编号，其中tty1表示Linux系统所在的本地第一个终端，而pts／0表示远程登录的第一个终端；FROM表示登录者的ip地址。。。

1.2 当我们使用who命令查看当前用户时，系统默认输出如下内容：

```shell
root     tty1         2016-09-18 14:57
root     pts/0        2016-09-19 22:12 (10.211.55.2)
```
内容类似于w命令的简化版，最后一列表示当前用户登录的时间。

## 2、查看用户登录记录命令last vs lastlog

1.1 last命令会根据登录的时间从距离现在最近的一次开始开始列举登录的用户信息，输出如下：

```shell
root     pts/0        10.211.55.2      Mon Sep 19 22:12   still logged in
root     pts/1        10.211.55.2      Mon Sep 19 10:58 - 11:53 (1+00:54)
root     pts/0        10.211.55.2      Sun Sep 18 14:57 - 11:10  (20:12)
root     tty1                          Sun Sep 18 14:57   still logged in
reboot   system boot  3.10.0-327.el7.x Sun Sep 18 14:57 - 00:04 (2+09:07)
root     pts/0        10.211.55.2      Wed Sep 14 09:54 - down   (10:11)
root     tty1                          Wed Sep 14 09:53 - 20:05  (10:12)
reboot   system boot  3.10.0-327.el7.x Wed Sep 14 09:53 - 20:05  (10:12)
[...]
reboot   system boot  3.10.0-327.el7.x Thu Sep  8 18:25 - 19:53 (5+01:28)
```

最后两列分别表示登录时间以及登出时间，still logged in表示当前用户仍在登录状态还没有退出。在last命令的结果中包含重启的相关信息。

1.2 lastlog命令会根据/etc/passwd文件中用户排列的顺序来一一列举每个用户最后一次登录该系统的相关信息，输出如下：

```shell
用户名           端口     来自             最后登陆时间
root             pts/0                     二 9月 20 12:20:51 +0800 2016
bin                                        **从未登录过**
[...]
sshd                                       **从未登录过**
taoxin           pts/0    10.211.55.2      五 8月  5 12:38:37 +0800 2016
nginx                                      **从未登录过**
mysql                                      **从未登录过**
apache                                     **从未登录过**
[...]
test             pts/0                     二 9月 20 12:20:24 +0800 2016
```

## 3、查看内建命令和外部命令的小技巧

**3.1 如何查看shell内建命令的相关信息**  
如果是shell自带的内建命令，当使用```man```命令查看时会列出一大推内建命令，不方便看，可以使用```help```命令来查看内建命令的相关信息，由于```help```命令本身也是内建命令，所以help命令的详细信息应该这样查看：```> help help```。

**3.2 如何查看一个命令是内建命令还是外部命令**  
可以使用```whereis```或者```which```命令查看一个命令是shell内建的命令还是外部命令，whereis命令是用来查找可执行命令的位置的，同时默认会列出帮助文件的位置，如果一个命令是内建命令，则不存在可执行文件的位置，只有帮助文件的位置，which同理。

**3.3 选项 --help**  
当我们使用```man command```时，命令的帮助文档是以英文显示的，如果系统安装的是中文的，则使用```command --help```代替```man command```，帮助文档会以中文显示。

## 4、禁止普通用户登录shell

当进行系统维护时，为了禁止除root用户外的其他用户登录，可以在```/etc/```目录下新建一个名为nologin的文件，当etc目录下存在该文件时，其余用户无法登录shell，在登录时会将/etc/nologin的内容打印，提示用户。

## 5、用户主组和附属组

每个用户只能有一个主组，多个附属组。可通过```usermod -g newgrp user```命令修改用户的主组，可通过```usermod -G newgrp user```给用户新增附属组。当用户新建一个文件时，该文件的组默认为用户的主组，可使用```newgrp groupname```命令修改要使用的组。在修改当前使用的组时需要输入密码，修改组密码可以使用```gpasswd groupname```命令。

下面的命令有很多交集(交集是指：相同的功能可以通过不同命令的一些附加选项实现)：

* groupadd
* gpasswd
* passwd
* useradd
* usermod

## 6、id命令

```id UID```：显示用户的UID，GID，主组和所有附属组

## 7、set VS printenv

* printenv：只能打印输出环境变量
* set：打印输出```环境变量+shell变量```

## 8、md5sum
MD5全称是报文摘要算法（Message-Digest Algorithm 5），此算法对任意长度的信息```逐位```进行计算，产生一个二进制长度为128位（十六进制长度就是32位）的“指纹”（或称“报文摘要”）。

该命令主要用于生成文件的md5指纹及文件md5指纹的校验。

* -b：将文件作为二进制文件来校验
* -t：将文件作为文本文件来校验
* -c：从指定文件读取md5指纹并与要校验的文件进行指纹比对

```shell
> md5sum -t test.txt > test.md5	// 生成md5指纹
> md5sum test.txt -c test.md5		// 指纹比对
```

## 9、find
[options]：

* -type： d、f、b、c、l(软链接)
* -name：
* -size：w(两个字节)、k、M、G
* -perm：匹配的文件或者目录的权限要为指定的权限
* -group：匹配的文件或者目录属于某个组
* -user：匹配的文件或者目录属于某个用户

[操作符]：

* -or：
* -and：
* -not：
* ()：

示例：

```shell
> find / \(-type f -not -perm 0600\) -or \(-type d -not -perm 0700\)
```

[预定义]：

* -delete：删除当前匹配的文件
* -exec: ```find / -name passwd -exec ls -l {} \;```将前面的结果传递给后面的命令执行

[小技巧]：

* 删除乱码文件：```ls -i```查找inode号码，然后通过```find / -inum inode -exec rm {} \;```

## 10、rev VS tac

* rev命令主要作用是反转每行的排序，反序
* tac命令主要作用是反转文件行到顺序，反序

## 11、stat

stat命令用于查看文件或者文件系统的的状态。

```shell
root@16:21:52 test]# stat abc
  File: 'abc'
  Size: 4         	Blocks: 8          IO Block: 4096   regular file
Device: ca01h/51713d	Inode: 273686      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2016-10-27 16:18:40.219123417 +0800
Modify: 2016-10-27 15:33:40.199579926 +0800
Change: 2016-10-27 15:33:40.210580161 +0800
 Birth: -
```

* Access：访问时间，cat等命令会修改这个值
* Modify：修改内容
* Change：修改权限的时间，chmod命令会更改这个值

### 12、cut

```shell
> cut -d : -f1,3 filename
```

* -d：指定分隔符
* -f：指定要筛选出来的列

## 13、sort

```shell
> sort -t : -k 3 -n filename
```

* -t：指定分隔符
* -k：指定排序的列
* -n：按数字排序而不是字符串