# Centos7 单用户模式修改root用户密码

## 单用户修改root用户密码步骤

1、重启OS，在grub菜单页面按“e“  
2、找到linux 16这一行，将这行中的```ro```替换为```rw init=/sysroot/bin/sh```,```Ctrl+x```启动OS进入单用户模式  
3、下面是进入单用户模式后操作：

```shell
> chroot /sysroot
> passwd root
> touch /.autorelabel
> exit
> reboot
```

## 不能重启OS

这个时候你会发现```reboot```命令不能重启系统了，系统提示错误```Failed to start poweroff.target: Transaction is destructive```,如果是虚拟机或者是不能接触到实体电源的话这下要懵逼了，至今没找到原因和解决办法，在stackoverflow上找到了一个比较粗暴的解决办法如下：

```shell
> echo 1 > /proc/sys/kernel/sysrq
> echo b > /proc/sysrq-trigger
```

这个方法可以在没办法解除实体电源键的情况下解决不能重启的问题，但是有人说用这种方法重启可能会导致OS上奔跑的数据库崩溃，暂未考证。

[以上解决方法来源(stackoverflow)](http://unix.stackexchange.com/questions/274761/bunsenlabs-debian-derrivative-wont-shut-down-failed-to-start-poweroff-target/304274#304274)

## 分析已知现象

在系统无法重启的情况下，使用```systemctl status reboot```查看会发现reboot.target的状态是死掉的，这时候```systemctl start reboot.target```是不起作用的，报错跟重启的错误一致。所以启动reboot服务然后使用该服务来关机的方法是行不通的。

## Author Info 🐨

* [GitHub](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>