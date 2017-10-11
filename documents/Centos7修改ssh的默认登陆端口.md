## Centos7修改ssh的默认登陆端口

### 第一步：修改/etc/ssh/sshd_config文件

```shell
# Port 22
Port 2222
```

### 第二步：修改SELinux

```shell
semanage port -l | grep ssh  // 查看当前selinux允许的端口
semanage port -a -t ssh_port_t -p tcp 2222  // 添加新端口到selinux允许的端口集
ssh_port_t                    tcp    20000, 22  // 添加成功后的输出
```

**注：有些版本的Centos7中没有安装semanage这个命令，可通过下面的命令安装：**

```shell
yum install policycoreutils-python -y
```

semanage这个命令在policycoreutils-python这个包中。

### 第三步：防火墙

Centos7中使用firewalld作为防火墙，iptables被替换掉了。

```shell
firewall-cmd --permanent --add-port=2222/tcp    // 添加允许放行的端口
firewall-cmd --permanent --query-port=1000/tcp  // 查看端口是否被放行
```

### 第四步：重启服务

```shell
systemctl restart sshd   // 重启sshd服务
firewall-cmd --reload    // 重启firewalld服务
```

### 注意

sshd服务和Firewalld服务需要重新启动之后才生效。

selinux在不需要的情况可以考虑关闭，因为selinux很麻烦。

当在sshd_config配置文件中禁用22端口后，在firewall-cmd命令中查询是否开启时会发现该端口是关闭的。