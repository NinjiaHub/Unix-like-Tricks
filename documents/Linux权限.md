# Linux权限

## 1、文件和目录的权限

文件的最高权限是执行权限x  
目录的最高权限是可读权限w

Linux中目录也是一个文件，目录文件中存储的内容是文件的名字、存储位置等信息，所以如果目录没有写权限则不能操作目录中的文件，因为操作目录中的文件需要修改文件的名字、存储位置等信息，所以不能删除、移动、重命名等操作。但是如果有文件的读权限和执行权限则可以读和执行。

Linux中的文件中存储的内容是文件的内容，是我们所见即所得的内容。

## 2、查看分区是否开启ACL权限

ACL权限主要是用来解决权限不够的问题的。例如user有rwx权限，组用户有rwx权限，其余人没有任何权限，但是要给一个既不是user用户也不是组用户的用户r-x权限这种情况。

可使用命令```dumpe2fs -h /dev/sda5```查看/dev/sda5分区的相关信息，结果：

```
[root@20:35:58 vm ~]# dumpe2fs -h /dev/mapper/VolGroup-lv_root
dumpe2fs 1.42.9 (28-Dec-2013)
Filesystem volume name:   <none>
Last mounted on:          /
Filesystem UUID:          cbf19209-b574-4515-a936-63b491dfa4b2
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent 64bit flex_bg sparse_super large_file huge_file uninit_bg dir_nlink extra_isize
Filesystem flags:         signed_directory_hash
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
```

ACL权限是否开启看其中的Default mount options：项，如果该项中有acl字段则表示该权限已经开启。
**注：dumpe2fs命令主要用来查询ext2/ext3/ext4类型文件系统中分区的相关信息。**

## 3、如何打开分区的ACL权限🅰️

**3.1 临时开启**  
如果是临时开启某个分区的ACL权限，则可以通过下面的命令开启：  
```mount -o remount,acl /```  
**注：mount命令的-o选项是用来特殊挂载的**

**3.2 永久开启**
修改/ect/fstab文件，找到指定的分区,在默认权限后加```,acl```开启acl权限，如下：

```
/dev/mapper/VolGroup-lv_root /                       ext4    defaults,acl        1 1
UUID=65a0a3bd-0a75-4368-a6bc-ec9967ae5873 /boot   ext4    defaults        1 2
/dev/mapper/VolGroup-lv_home /home                ext4    defaults        1 2
/dev/mapper/VolGroup-lv_swap swap                 swap    defaults        0 0
```
修改完配置文件后，有两种方法使配置生效：第一种是重启系统，让系统在启动过程中读取该配置文件时开始ACL权限；第二种方法是使用mount命令重新挂载该分区，在重新挂载时也会读取该配置文件开启ACL权限，```mount -o remount /```。

## 4、查看文件的ACL权限

```
$ getfacl filename
```

## 5、设置文件的ACL权限

```
$ setfacl -m 'u:username:rwx' filename/dirname
$ setfacl -m 'g:gname:rwx' filename/dirname
```

**注意：setfacl命令有一个-R选项，使用该选项可以给用户递归地设置目录的ACL权限。这样设置有方便的地方，但是要小心权限溢出，即不要给文件设置了执行权限。**

## 6、最大有效权限

当我们使用getfacl命令查看文件的acl权限时，其中的mask选项的值即为最大有效权限。我们赋予用户或者用户组的acl权限是否有效取决于mask的值，我们给予用户的权限与mask的值进行逻辑与操作的结果就是该用户可以获得的最大权限。

可通过下面的命令指定最大有效权限：

```shell
$ setfacl -m m:rx filename/dirname
```

## 7、删除文件的ACL权限

```shell
$ setfacl -x u:username filename
$ setfacl -b filename
```

-x：删除指定用户的acl权限
-b：删除该文件赋予的所有用户和组的acl权限

## 8、给目录设置默认权限

```shelll
$ setfacl -m d:u:usrname:rwx -R dirname
```
可使用上面的命令解决权限溢出的问题，当给一个目录设置了默认权限后，该目录中新建的目录的mask默认为rwx，而新建文件的mask默认为rw，从而可以避免递归ACL权限造成的权限溢出问题。

## 9、sudo权限

可以通过使用```visudo```命令来给普通用户赋予管理员权限。visudo命令会打开sudo权限的配置文件，我们可以在其中加入配置信息从而达到给普通用户赋予管理员权限的目的。

```
username			ALL=(ALL)								command(绝对命令)
要赋予权限的UID		被管理主机的地址=(username可使用的身份)		授权该用户使用的命令
```

在给用户赋予权限时要尽量限制用户的权限范围，command命令应该尽量具体以便于限制用户权限。

```
username ALL=/usr/bin/passwd [a-zA-Z]+, !/usr/bin/passwd "", !/usr/bin/passwd root
```

同给用户赋予权限，可以给组赋予管理员权限，只需要把```username```改为```%groupname```，后面的部分跟给用户赋予权限一致。

## 10、SetUID(只能针对文件)

**作用：在命令执行期间执行该命令的用户获得该文件属主的身份**

* 只有具有可执行权限的二进制文件才可以设置SUID权限
* 命令执行者要对文件具有可执行权限
* SetUID权限只在执行过程中有效，命令执行结束后执行者不再具有文件属主的身份

**设置方法：**

```shell
$ chmod u+s filename
$ chmod 4755 filename
```
同普通权限，权限按ugo顺序排列，所以u为4，g为2，o为1。

**例子：**

/usr/bin/passwd程序即有SetUID权限，因为普通用户不能修改/etc/passwd文件，所以在该命令执行时普通用户需要具有root权限才能将修改后的密码写入/etc/passwd文件。

> **注：如果文件属主对要赋予SetUID权限的文件没有x权限，则该文件只会获得S权限，s=S+x，所以其余用户在执行该文件时不会获得属主的身份。**

**应该尽量限制SetUID权限的使用，使用不当会很不安全。**

## 11、SetGID权限(可针对文件和目录)

**针对文件时：**
> 针对文件时，其作用跟SetUID非常类似，区别是在普通用户执行具有SetGID权限的文件时会获得该文件属组的身份，即用户在执行该文件时其有效属组会改变为该文件的属组，其余跟SetUID一致。

**针对目录时：**
> 1、用户必须具有该目录的rx权限，否则不能cd进该目录  
> 2、用户在该目录中的有效属组为该目录的属组  
> 3、如果用户对该目录有w权限，则该用户在该目录下新建的文件的属组为该目录的属组

## 12、Sticky BIT(仅对目录有效)

**作用：具有SBIT权限的目录，除root用户外，其余用户只能删除自己建立的文件，如果该目录没有SBIT权限且其余用户具有w权限，则其余所有用户都可以删除该目录下的任何文件。**

要求：SBIT权限要求用户具有目录的wx权限

**设置方法：**

```shell
$ chmod o+t dirname
$ chmod o-t dirname
```

## 13、chattr(不可改变位)
**设置方法：**

```shell
$ chattr +i filename/dirname
$ chattr +a filename/dirname
```

**作用：**
> 1、i权限会锁定文件或者目录，具有i权限的文件不允许修改内容，具有i权限的目录不允许删除、新增和重命名文件  
> 2、a权限只允许追加内容，具有a权限的文件只允许向其中追加内容，具有a权限的目录只允许向其中新建文件

**注意：由于使用vim编辑器编辑文件时，系统不容易判断是向其中追加了内容还是修改删除了内容，所以具有a权限的文件不允许使用vim编辑器编辑该文件。只允许通过重定向的方式向文件中追加内容。**

**查看文件或者目录是否具有不可改变位：**

```shell
$ lsattr -d dirname  
$ lsattr filename
```
