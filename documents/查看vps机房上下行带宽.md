# 测试服务器机房带宽

```shell
$ wget -O speedtest-cli https://raw.github.com/sivel/speedtest-cli/master/speedtest_cli.py

$ chmod +x speedtest-cli
$ ./speedtest-cli
```

第一行下载speedtest文件，该文件是一个使用python语言写的脚本，所以在使用前，请确保你的机器上安装了python。

第二行给下载的speedtest-cli.py文件赋予可执行权限。

第三行执行该文件。

输出信息如下：

```shell
Retrieving speedtest.net configuration...
Testing from DigitalOcean (138.68.31.68)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Sonic.net, Inc (San Jose, CA) [11.20 km]: 4.37 ms
Testing download speed................................................................................
Download: 1055.20 Mbit/s
Testing upload speed................................................................................................
Upload: 1125.49 Mbit/s
```

可以看出下行带宽为1055.20 Mbit/s，上行带宽为1125.49 Mbit/s。

## 进阶

### 1、分享

```shell
$ ./speedtest-cli --share
```
	
这条命令会产生一张图像。如果是在命令行中运行，则会打印一个图像的url。下面的图像是我自己租的DigitalOcean的服务器的贷款测试速度。

![bandwidth](http://www.speedtest.net/result/5012589337.png)

### 2、speedtest.net服务器列表

```shell
$ ./speedtest-cli --list
```
	
该命令会按照距离列出 所有的speedtest.net服务器。

## 其他

[speedtest官网](http://beta.speedtest.net/)

## 声明

部分内容来自网络，如有版权问题请联系作者。

侵删。

如有错误或者不恰当的地方，敬请指正。

作者邮箱：web.taox@gmail.com。