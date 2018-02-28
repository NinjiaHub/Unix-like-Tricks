# shell条件判断与流程控制

## case语句

```shell
case $variable in
	a ) echo "a";;
	b ) echo "b";;
	* ) echo "*";;
esac
```

默认情况下case语句匹配到一个匹配内容后会终止匹配后续内容，退出case语句。如果不只想匹配一个内容，可能有多个匹配内容，可以使用```;;&```来结束匹配语句，这样就可以在匹配一个语句后继续下一次匹配，如下：

```shell
case $variable in
	[[:lower:]] ) echo "lower";;&
	[[:alpha:]] ) echo "alpah";;
	* ) echo "*";
esac
```

**<p style="color:red;">注：在使用```;;&```时要注意```*```这个通配符，否则所有的输入都会匹配```*```，然后执行```*```后面的command。</p>**

## 1、按文件类型判断(常用)

* -f：文件存在且为普通文件
* -e：文件存在且为目录或者普通文件
* -d：文件存在且为目录
* -s：文件存在且不为空
* -L：文件存在且为软连接文件
* -ef：两个文件具有相同的索引(即硬链接)```file1 -ef file2```
* -b：块设备文件
* -c：字符文件

## 2、按权限判断

* -r：文件存在且可读
* -w：文件存在且可写
* -x：文件存在且可执行
* -u：文件存在且拥有SetUID权限
* -g：文件存在且拥有SetGID权限
* -k：文件存在且拥有Set Bit权限

## 3、文件比较

* file1 -nt file2: 判断修改时间
* file1 -ot file2：判断修改时间
* file1 -ef file2: 判断inode是否一致，即判断是否为同一个节点，判断是否为硬链接

## 4、整数比较

* -gt：>
* -lt：<
* -ge：>=
* -le: <=
* -eq: ==
* -ne: !=

## 5、字符串判断

* -z string：判断字符串是否为空
* -n string：判断字符串是否不为空
* s1 == s2
* s1 != s2

## 6、多重判断

* condition1 -a condition2：条件1、2都为真结果为真```[-n "$variable" -a 5 -gt 6]```
* condition1 -o condition2: 或
* !: 取非

## 作者信息 🦊

* [GitHub](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>