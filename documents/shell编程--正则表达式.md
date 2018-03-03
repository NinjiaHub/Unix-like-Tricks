# shell编程--正则表达式

## 1、正则 vs 通配符

正则表达式是包含匹配，通配符是完全匹配。

## 2、范围匹配

```
grep "sa\{3,5\}" test.txt
```

**注：使用范围匹配时，一定要在{}花括号前加上```\```。**

## 3、POSIX字符集

```shell
元字符(Metacharacter)	  匹配(Matches)

[[:alnum:]]				字母和数字
[[:alpha:]]				字母
[[:blank:]]				空格和制表符
[[:cntrl:]]				控制字符
[[:digit:]]				数字
[[:graph:]]				非空白字符
[[:lower:]]				小写字母
[[:print:]]				类似[[:graph:]]，但是包含空白字符
[[:punct:]]				标点符号
[[:space:]]				空白字符
[[:upper:]]				大写字母
[[:xdigit:]]			十六进制中容许出现的数字(例如 0-9a-fA-f)
```

## 作者信息 🐼

* [GitHub](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>