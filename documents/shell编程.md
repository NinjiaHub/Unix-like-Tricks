## Linux Shell编程

### 在shell脚本中声明局部变量

```shell
#!/bin/bash

function func_name {
	local foo=a
}
```

### 在shell中，变量的默认类型都是字符串类型

声明变量：variable=value，“=”左右不能有空格  
使用变量：```$variable```

变量排错：```echo $a```：如果变量a没有声明，当使用该变量时默认输出空白，可使用set -u来排错，当调用```set -u```命令后，如果使用的变量没有声明，则会报错，如下：

```
[root@18:20:02 vm shellscripts]# echo $name
-bash: name: unbound variable
```

### 变量的分类

* 用户自定义的变量
* 环境变量：主要保存与系统操作环境相关的变量
* 位置参数变量：主要用于向脚本中传递参数{$*, $@, $n($0:命令本身, ${1-n}), $#(代表参数个数)}
* 预定义变量：系统预先定义好的变量，变量名和作用固定，不能更改

### 环境变量

环境变量是全局变量，可以在所有shell及其子shell中生效；用户自定义的变量是局部变量，只能在当前shell中使用，默认不能在子shell中使用，不过可以使用```export variable```命令将变量变为全局变量，使其可以在子shell中使用。**注意：在子shell中声明的全局变量不能在父shell中使用，同时，可以在子shell中使用unset命令删除父shell中声明的变量，但是只能删除当前shell中的同名变量，当退出当前shell回到父shell时，仍可以访问在zishell中删除的变量。**

### set vs env

set命令用来查看所有的变量  
env命令用来查看所有的环境变量

### PS1 VS PS2 VS PS4

[更详细的阐述：The Geek Stuff](http://www.thegeekstuff.com/2008/09/bash-shell-take-control-of-ps1-ps2-ps3-ps4-and-prompt_command/)

* PS1:光标前的信息提示
* PS2: 当命令在一行中显示不完时的信息提示符号

```
[root@18:43:56 vm shellscripts]# ls \
>
```
当命令在一行中放不下时，我们可以通过使用```\```来换行输入。

* PS3:用在select中，用以自定义select选项中的提示信息

```
select item in a b c d "exit"; do
  6     case $item in
  7     a)
  8         echo "a";;
  9     b)
 10         echo "b";;
 11     c)
 12         echo "c";;
 13     d)
 14         echo "d";;
 15     exit)
 16         exit;;
 17     esac
 18 done
```

上面的脚本在执行时如下：
 
```
 [root@23:23:43 vm shellscripts]# ./select.sh
1) a
2) b
3) c
4) d
5) exit
#?
```
输入提示的地方只是```#?```，这个是PS3的默认值，但是让人不知道什么意思，我们可以通过定制PS3来修改这个提示符，见下面：

```
PS3=“请选择数字（1-5）： ”
```

结果：

```
[root@23:26:34 vm shellscripts]# ./select.sh
1) a
2) b
3) c
4) d
5) exit
请选择数字（1-5）： 
```

* PS4: 用于脚本的debug模式中的信息提示符

```
ramesh@dev-db ~> cat ps4.sh

set -x
echo "PS4 demo script"
ls -l /etc/ | wc -l
du -sh ~

ramesh@dev-db ~> ./ps4.sh

++ echo 'PS4 demo script'
PS4 demo script
++ ls -l /etc/
++ wc -l
243
++ du -sh /home/ramesh
48K     /home/ramesh
[Note: This displays the default "++" while tracing the output using set -x]
```

**注：```set -x```用于开启脚本的debug模式，会在每行脚本执行时打印命令及其参数，PS4默认会在命令及参数行前加```++```,可通过修改PS4让提示符更明显。**

```
amesh@dev-db ~> cat ps4.sh

export PS4='$0.$LINENO+ '
set -x
echo "PS4 demo script"
ls -l /etc/ | wc -l
du -sh ~

ramesh@dev-db ~> ./ps4.sh
./ps4.sh.3+ echo 'PS4 demo script'
PS4 demo script
./ps4.sh.4+ ls -l /etc/
./ps4.sh.4+ wc -l
243
../ps4.sh.5+ du -sh /home/ramesh
48K     /home/ramesh
[Note: This displays the modified "{script-name}.{line-number}+"
       while tracing the output using set -x]
```

其中，\$LINENO代表当前执行脚本的行号。

### declare命令声明变量类型

declare [+/-] [options] variable  
+：取消variable的类型  
-：增加variable的类型  
```declare -x variable```命令会将变量声明为环境变量，```export```命令其实是调用的```declare -x```命令。

### 数值运算

* $(( $a + $b))
* $[ $a + $b ]

**注：当在脚本中使用```$(())```对变量进行运算时，变量名字前的```$```符号可以省略。**

### trap

该命令可用于在shell脚本中捕获信号，从而针对指定的信号处理指定的任务。例如在脚本运行过程中用户使用了```Ctrl+c```快捷键停止脚本运行，如果该脚本运行过程中存在临时文件，应该捕获该信号删除临时文件然后终止脚本运行。

```shell
#!/bin/bash

clean_up() {

	# Perform program exit housekeeping
	# Optionally accepts an exit status
	rm -f $TEMP_FILE
	exit $1
}

trap clean_up SIGHUP SIGINT SIGTERM
```

当系统产生信号并且发送到bash时，trap命令会捕获该信号并且可以执行指定的命令。但是需要注意的是trap命令不能捕获```SIGKILL```信号，因为当kernel收到该信号时会直接杀死指定的进程，shell不会收到该信号，从而trap命令也不能捕获该信号。