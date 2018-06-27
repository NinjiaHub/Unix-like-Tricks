# autojump

命令行工具，会对用户输入的目录名字进行模糊匹配，用于在文件系统中快速跳转。该工具会自己维护一个数据中，其中保存着用户访问过的每一个目录，并且会根据访问次数修改目录的权重，以便于在模糊匹配时匹配访问次数最多的目录。

由于是对用户访问过的目录进行记录分析的，所以在使用该命令之前需要先跳转过目录，数据库中有记录之后才能使用；否则只会跳转当前目录。

## install

### Linux

可以使用 **yum**、**apt-get** 等包管理工具下载安装。

### OS X

建议使用 Homebrew 进行安装：

```cli
brew install autojump
```

### Windows

可以点击 [下载](https://mridgers.github.io/clink/) 下载安装。

### 手动安装

获取源文件：

```cli
git clone git://github.com/joelthelion/autojump.git
```

执行安装脚本：

```cli
cd autojump
./install.py or ./uninstall.py
```

## 安装输出

下面是 **autojump** 安装结束后的输出：

```terminal
Add the following line to your ~/.bash_profile or ~/.zshrc file (and remember
to source the file to update your current session):
  [ -f /usr/local/etc/profile.d/autojump.sh ] && . /usr/local/etc/profile.d/autojump.sh

If you use the Fish shell then add the following line to your ~/.config/fish/config.fish:
  [ -f /usr/local/share/autojump/autojump.fish ]; and source /usr/local/share/autojump/autojump.fish

zsh completions have been installed to:
  /usr/local/share/zsh/site-functions
```

**autojump** 安装结束后并不能立即使用，需要执行 `autojump.sh` 这个 shell 脚本来完成一些设置；上面的输出解释了如何在不同的 shell 中执行该脚本。

## 使用方法

autojump 封装了一个快捷命令 `j`，`j` 等同于 autojump 命令，方便用户输入；所有可以使用 `autojump` 的地方，都可以使用 `j` 来代替。

* `j foo` - 跳转到权重最大的目录 `foo`
* `jc foo` - 跳转到当前目录的字目录 `foo`
* `jo foo` - 在系统中的文件管理窗口（Mac 中的 finder，GNOME 中的 Nautilus，Windows 中的文件夹等）中打开 `foo` 目录
* `jco foo` - 在文件管理窗口中打开当前目录的子目录 `foo`
* `autojump --help` - 帮助文档

## Repo

[wting/autojump](https://github.com/wting/autojump)

## Author 🦐

* [GitHub](https://github.com/Tao-Quixote)
* Email: <web.taox@gmail.com>