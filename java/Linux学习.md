Linux学习



使用Ubuntu16.04 server



# 远程控制

## 检查软件是否安装

```
apt-cache policy openssh-client openssh-server
```

## 安装服务端

```
apt-get install openssh-server
```

## 安装客户端

```
apt-get install openssh-client
```

## Windows远程连接软件

XShell



# Linux 的目录结构

| 目录 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| bin  | 存放二进制可执行文件(ls,cat,mkdir等)                         |
| boot | 存放用于系统引导时使用的各种文件                             |
| dev  | 用于存放设备文件                                             |
| etc  | 存放系统配置文件                                             |
| home | 存放所有用户文件的根目录                                     |
| lib  | 存放跟文件系统中的程序运行所需要的共享库及内核模块           |
| mnt  | 系统管理员安装临时文件系统的安装点                           |
| opt  | 额外安装的可选应用程序包所放置的位置                         |
| proc | 虚拟文件系统，存放当前内存的映射                             |
| root | 超级用户目录                                                 |
| sbin | 存放二进制可执行文件，只有root才能访问                       |
| tmp  | 用于存放各种临时文件                                         |
| usr  | 用于存放系统应用程序，比较重要的目录/usr/local 本地管理员软件安装目录 |
| var  | 用于存放运行时需要改变数据的文件                             |



# Linux操作文件目录

| 命令  | 说明                               | 语法                                          | 参数  | 参数说明                           |
| ----- | ---------------------------------- | --------------------------------------------- | ----- | ---------------------------------- |
| ls    | 显示文件和目录列表                 | ls [-alrtAFR] [name…]                         |       |                                    |
|       |                                    |                                               | -l    | 列出文件的详细信息                 |
|       |                                    |                                               | -a    | 列出当前目录所有文件，包含隐藏文件 |
| mkdir | 创建目录                           | mkdir [-p] dirName                            |       |                                    |
|       |                                    |                                               | -p    | 父目录不存在情况下先生成父目录     |
| cd    | 切换目录                           | cd [dirName]                                  |       |                                    |
| touch | 生成一个空文件                     |                                               |       |                                    |
| echo  | 生成一个带内容文件                 | echo abcd > 1.txt，echo 1234 >> 1.txt         |       |                                    |
| cat   | 显示文本文件内容                   | cat [-AbeEnstTuv] [–help] [–version] fileName |       |                                    |
| cp    | 复制文件或目录                     | cp [options] source dest                      |       |                                    |
| rm    | 删除文件                           | rm [options] name…                            |       |                                    |
|       |                                    |                                               | -f    | 强制删除文件或目录                 |
|       |                                    |                                               | -r    | 同时删除该目录下的所有文件         |
| mv    | 移动文件或目录                     | mv [options] source dest                      |       |                                    |
| find  | 在文件系统中查找指定的文件         |                                               |       |                                    |
|       |                                    |                                               | -name | 文件名                             |
| grep  | 在指定的文本文件中查找指定的字符串 |                                               |       |                                    |
| tree  | 用于以树状图列出目录的内容         |                                               |       |                                    |
| pwd   | 显示当前工作目录                   |                                               |       |                                    |
| ln    | 建立软链接                         |                                               |       |                                    |
| more  | 分页显示文本文件内容               |                                               |       |                                    |
| head  | 显示文件开头内容                   |                                               |       |                                    |
| tail  | 显示文件结尾内容                   |                                               |       |                                    |
|       |                                    |                                               | -f    | 跟踪输出                           |



# Linux系统管理操作

| 命令     | 说明                                         |
| -------- | -------------------------------------------- |
| stat     | 显示指定文件的相关信息,比ls命令显示内容更多  |
| who      | 显示在线登录用户                             |
| hostname | 显示主机名称                                 |
| uname    | 显示系统信息                                 |
| top      | 显示当前系统中耗费资源最多的进程             |
| ps       | 显示瞬间的进程状态                           |
| du       | 显示指定的文件（目录）已使用的磁盘空间的总量 |
| df       | 显示文件系统磁盘空间的使用情况               |
| free     | 显示当前内存和交换空间的使用情况             |
| ifconfig | 显示网络接口信息                             |
| ping     | 测试网络的连通性                             |
| netstat  | 显示网络状态信息                             |
| clear    | 清屏                                         |
| kill     | 杀死一个进程                                 |

# Linux 压缩命令

### tar

| 命令 | 语法                                        | 参数 | 参数说明                        |
| ---- | ------------------------------------------- | ---- | ------------------------------- |
| tar  | tar [-cxzjvf] 压缩打包文档的名称 欲打包目录 |      |                                 |
|      |                                             | -c   | 建立一个归档文件的参数指令      |
|      |                                             | -x   | 解开一个归档文件的参数指令      |
|      |                                             | -z   | 是否需要用 gzip 压缩            |
|      |                                             | -j   | 是否需要用 bzip2 压缩           |
|      |                                             | -v   | 压缩的过程中显示文件            |
|      |                                             | -f   | 使用档名，在 f 之后要立即接档名 |
|      |                                             | -tf  | 查看归档文件里面的文件          |

**例子：**

- 压缩文件夹：`tar -zcvf test.tar.gz test\`
- 解压文件夹：`tar -zxvf test.tar.gz`

### gzip

| 命令 | 语法                               | 参数 | 参数说明                                                     |
| ---- | ---------------------------------- | ---- | ------------------------------------------------------------ |
| gzip | gzip [选项] 压缩（解压缩）的文件名 |      |                                                              |
|      |                                    | -d   | 解压缩                                                       |
|      |                                    | -l   | 对每个压缩文件，显示压缩文件的大小，未压缩文件的大小，压缩比，未压缩文件的名字 |
|      |                                    | -v   | 对每一个压缩和解压的文件，显示文件名和压缩比                 |
|      |                                    | -num | 用指定的数字num调整压缩的速度，-1或–fast表示最快压缩方法（低压缩比），-9或–best表示最慢压缩方法（高压缩比）。系统缺省值为6 |

说明：压缩文件后缀为 gz

### bzip2

| 命令  | 语法         | 参数 | 参数说明                                                     |
| ----- | ------------ | ---- | ------------------------------------------------------------ |
| bzip2 | bzip2 [-cdz] |      |                                                              |
|       |              | -d   | 解压缩                                                       |
|       |              | -z   | 压缩参数                                                     |
|       |              | -num | 用指定的数字num调整压缩的速度，-1或–fast表示最快压缩方法（低压缩比），-9或–best表示最慢压缩方法（高压缩比）。系统缺省值为6 |

说明：压缩文件后缀为 bz2



# Linux 编辑器

*李卫民 发表于 2018-05-12*

### vim

#### 运行模式

编辑模式：等待编辑命令输入

插入模式：编辑模式下，输入 `i` 进入插入模式，插入文本信息

命令模式：在编辑模式下，输入 `:` 进行命令模式

#### 命令

`:q` 直接退出vi

`:wq` 保存后退出vi ，并可以新建文件

`:q!` 强制退出

`:w file` 将当前内容保存成某个文件

`:set number` 在编辑文件显示行号

`:set nonumber` 在编辑文件不显示行号



# Linux 软件包管理

*李卫民 发表于 2018-05-12*

APT(Advanced Packaging Tool) 是 Debian/Ubuntu 类 Linux 系统中的软件包管理程序, 使用它可以找到想要的软件包, 而且安装、卸载、更新都很简便；也可以用来对 Ubuntu 进行升级; APT 的源文件为 `/etc/apt/` 目录下的 `sources.list` 文件。

### 修改数据源

由于国内的网络环境问题，我们需要将 Ubuntu 的数据源修改为国内数据源，操作步骤如下：

#### 查看系统版本

```
lsb_release -a
```

输出结果为

```
No LSB modules are available.
Distributor ID:    Ubuntu
Description:    Ubuntu 16.04 LTS
Release:    16.04
Codename:    xenial
```

**注意：** Codename 为 `xenial`，该名称为我们 Ubuntu 系统的名称，修改数据源需要用到该名称

#### 编辑数据源

```
vi /etc/apt/sources.list
```

删除全部内容并修改为

```
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
```

#### 更新数据源

```
apt-get update
```

### 常用 APT 命令

#### 安装软件包

```
apt-get install packagename
```

#### 删除软件包

```
apt-get remove packagename
```

#### 更新软件包列表

```
apt-get update
```

#### 升级有可用更新的系统（慎用）

```
apt-get upgrade
```

### 其它 APT 命令

#### 搜索

```
apt-cache search package
```

#### 获取包信息

```
apt-cache show package
```

#### 删除包及配置文件

```
apt-get remove package --purge
```

#### 了解使用依赖

```
apt-cache depends package
```

#### 查看被哪些包依赖

```
apt-cache rdepends package
```

#### 安装相关的编译环境

```
apt-get build-dep package
```

#### 下载源代码

```
apt-get source package
```

#### 清理无用的包

```
apt-get clean && apt-get autoclean
```

#### 检查是否有损坏的依赖

```
apt-get check
```





# Linux 安装 Java

*李卫民 发表于 2018-05-12*

### Linux 安装 Java

此处以 JDK 1.8.0_152 为例

#### 下载地址

<http://www.oracle.com/technetwork/java/javase/downloads/index.html>

#### 解压缩并移动到指定目录

##### 解压缩

```
tar -zxvf jdk-8u152-linux-x64.tar.gz
```

##### 创建目录

```
mkdir -p /usr/local/java
```

##### 移动安装包

```
mv jdk1.8.0_152/ /usr/local/java/
```

##### 设置所有者

```
chown -R root:root /usr/local/java/
```

#### 配置环境变量

##### 配置系统环境变量

```
nano /etc/environment
```

##### 添加如下语句

```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
export JAVA_HOME=/usr/local/java/jdk1.8.0_181
export JRE_HOME=/usr/local/java/jdk1.8.0_181/jre
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
```

##### 配置用户环境变量

```
nano /etc/profile
```

##### 添加如下语句

```
if [ "$PS1" ]; then
  if [ "$BASH" ] && [ "$BASH" != "/bin/sh" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

export JAVA_HOME=/usr/local/java/jdk1.8.0_181
export JRE_HOME=/usr/local/java/jdk1.8.0_181/jre
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HOME/bin

if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi
```

##### 使用户环境变量生效

```
source /etc/profile
```

#### 测试是否安装成功

```
root@UbuntuBase:/usr/local/java# java -version
java version "1.8.0_152"
Java(TM) SE Runtime Environment (build 1.8.0_152-b16)
Java HotSpot(TM) 64-Bit Server VM (build 25.152-b16, mixed mode)
```

#### 为其他用户更新用户环境变量

```
su lusifer
source /etc/profile
```