# shell编程--读取用户输入

## read命令

当read命令后没有指定接收参数的变量时，shell变量REPLY会接收所有的参数。

如果read命令指定了多个变量接收参数，如果赋值个数比变量少，则没有赋值的变量的值为空；如果赋值个数比变量多，则最后一个变量会接收所有的剩余值。

## <<<

下面的代码将```/etc/passwd```文件中记录的root用户的信息使用```:```分隔开，然后通过read命令赋值给多个变量：

```shell
> $info=$(grep "root" /etc/passwd)
> old_ifs=$IFS
> IFS=:
> read user passwd uid gid desc home bash <<< $info
> IFS=$old_ifs
```

注意上面使用的```<<<```,在这里是不能使用管道符将```$info```的内容赋值给read后七个变量。因为管道符会在管道后的命令执行时创建子shell，管道后的命令将在子shell中执行，该命令执行结束后子shell即销毁，同时read命令后的七个变量跟随子shell销毁，所以通过管道符执行后当前shell中不存在read命令后的七个变量。

```shell
> grep "root" /etc/passwd | IFS=:; read user passwd uid gid desc home bash
```

**<p style="color:red;">上面的代码执行完后当前shell中不存在read命令后的七个变量。</p>**

## <<-

输入重定向中，```<<-```符号可以忽略多行输入中tab自动补全的功能，转而实现在多行输入中添加制表符。

## 组命令 & 子shell

```shell
{ command1; command2; ... }
(command1; command2;...)
```

组命令在当前shell中执行，子shell在当前shell的子shell中执行命令。**组命令更快且占用更少的内存。**除非有特殊要求，一般使用组命令。

## 进程替换

管道线中的命令总是在子shell中运行，任何给变量赋值的命令在执行结束后变量都被销毁，相当于没有效果。

进程替换是一种奇异的展开方式，可以把子shell的输出结果当成一个用于重定向的普通文件。

```shell
<(list)
```

```shell
> cat < <(grep -E "^root" /etc/passwd)
```

## 作者信息 🐰

* [GitHub](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>