# 环境安装

## 使用脚本自动安装

在测试或开发环境中 Docker 官方为了简化安装流程，提供了一套便捷的安装脚本，Ubuntu 系统上可以使用这套脚本安装：

```
#移动云,使用Centos7.6进行

$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh --mirror AzureChinaCloud
```



## 启动服务

```
service docker start
```



## Docker 镜像加速器

请在 `/etc/docker/daemon.json` 中写入如下内容（如果文件不存在请新建该文件）

```
{
  "registry-mirrors": ["https://tbyju4u1.mirror.aliyuncs.com"]
}

#阿里云加速配置页面
#https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors?accounttraceid=4de3582aef454ae3a918e6c0e38b482dotjf
```

重启Docker服务

```python
#systemctl restart docker
#或者
#service docker restart

$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

**检查加速器是否生效**

配置加速器之后，如果拉取镜像仍然十分缓慢，请手动检查加速器配置是否生效，在命令行执行 `docker info`，如果从结果中看到了如下内容，说明配置成功。

```
docker info

#如果出现以下信息则成功
Registry Mirrors:
 https://registry.docker-cn.com/
```



## 安装Jupyter Notebook

### 安装基础版

```
docker run -it -p 8888:8888 jupyter/datascience-notebook
```

### 安装TensorFlow版

```
docker run -it -p 8888:8888 tensorflow/tensorflow:nightly-py3-jupyter
```

### 设置密码

```python
#进入docker容器
docker exec id -it /bin/bash
#设置密码
jupyter notebook password

然后重启docker
```



### 添加映射

```
还需要一个~v 映射命令
```



### 开启:自动补全

```python
#进入Docker容器后,使用命令
pip install jupyter_contrib_nbextensions  -i https://mirrors.aliyun.com/pypi/simple/
#开始安装nbextension
jupyter contrib nbextension install --user --skip-running-check

在标签页"nbextension",启动"Hinterland"

#参考网址
https://www.jianshu.com/p/0ab80f63af8a
```

### 安装皮肤

```python
#安装
pip install jupyterthemes -i https://mirrors.aliyun.com/pypi/simple/

#安装主题
jt -t monokai

```





# 镜像

## 镜像查询:docker search

https://hub.docker.com/

```
docker search ubuntu
```



## 获取镜像:docker pull

### 命令

从 Docker 镜像仓库获取镜像的命令是 `docker pull`。其命令格式为：

```
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```

比如：

```
$ docker pull ubuntu:16.04
```

## 运行镜像:docker run

有了镜像后，我们就能够以这个镜像为基础启动并运行一个容器。以上面的 `ubuntu:16.04` 为例，如果我们打算启动里面的 `bash` 并且进行交互式操作的话，可以执行下面的命令。

```
$ docker run -it --rm  ubuntu:16.04 bash
```

`docker run` 就是运行容器的命令，我们这里简要的说明一下上面用到的参数。

- `-it`：这是两个参数，一个是 `-i`：交互式操作，一个是 `-t` 终端。我们这里打算进入 `bash` 执行一些命令并查看返回结果，因此我们需要交互式终端。
- `--rm`：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 `docker rm`。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 `--rm` 可以避免浪费空间。
- `ubuntu:16.04`：这是指用 `ubuntu:16.04` 镜像为基础来启动容器。
- `bash`：放在镜像名后的是**命令**，这里我们希望有个交互式 Shell，因此用的是 `bash`。



## 列出镜像:docker  image ls

```
docker image ls
```

## 删除镜像:docker image rm

```
$ docker image rm [选项] <镜像1> [<镜像2> ...]
```

其中，`<镜像>` 可以是 `镜像短 ID`、`镜像长 ID`、`镜像名` 或者 `镜像摘要`。



# 容器仓库（Docker Hub)

登录：docker login

注销：docker logout

## 拉取镜像：docker pull

## 推送镜像：docker push

# 容器

## 查看容器:docker ps [-a]

正在运行

```
docker ps
```

-a查询所有，包括停止的



## 删除容器：docker rm

```
docker rm 容器ID
```



## 新建并启动:docker run

启动容器有两种方式，`一种是基于镜像新建一个容器并启动`，另外一个是将在终止状态（`stopped`）的容器重新启动。

所需要的命令主要为 `docker run`。

例如，下面的命令输出一个 “Hello World”，之后终止容器。

```
$ docker run ubuntu:14.04 /bin/echo 'Hello world'
Hello world
```

### 重命名:--name ***

```
-- name myUbuntu
```

### 端口映射: -p  宿主端口：容器端口

```
-p 8081:8080
```

宿主端口：容器端口

### 守护状态运行:-d

```
-d
```

### 挂载：-v 主机目录：容器目录

```
-v $PWD/test:/usr/local/tomcat/webapps/test
```

将主机中当前目录下的test挂载到容器的/test

### 交互：-it bash

下面的命令则启动一个 bash 终端，允许用户进行交互。

```
$ docker run -t -i ubuntu:14.04 /bin/bash
root@af8bae53bdd3:/#
```

其中，`-t` 选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上， `-i` 则让容器的标准输入保持打开。

## 停止容器:docker stop

```
docker stop
```

## 进入容器:docker exec

```
docker exec
```

### 交互式进入:-it *** bash

```
docker exec -it  myUbuntu  bash
```



# 数据卷

`数据卷` 是一个可供一个或多个容器使用的特殊目录





# Docker 构建 Tomcat

```
docker run --name tomcat -p 8080:8080 -v $PWD/test:/usr/local/tomcat/webapps/test -d tomcat
```



# Docker 构建 MySQL

```
docker run -p 3306:3306 --name mysql \
-v /usr/local/docker/mysql/conf:/etc/mysql \
-v /usr/local/docker/mysql/logs:/var/log/mysql \
-v /usr/local/docker/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql
```



# 常用命令

## 从主机复制文件到容器

```
sudo docker cp host_path containerID:container_path
```

## 从容器复制文件到主机

```
sudo docker cp containerID:container_path host_path
```



# Docker Compose 

## 安装与卸载

### 使用Curl下载

在 Linux 上的也安装十分简单，从 [官方 GitHub Release](https://github.com/docker/compose/releases) 处直接下载编译好的二进制文件即可。

```
$ sudo curl -L https://github.com/docker/compose/releases/download/1.17.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$curl -L https://github.com/docker/compose/releases/download/1.25.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

**如果出现无法下载问题，hosts文件添加**

```python
#sudo vim /etc/hosts   修改hosts文件
#sudo /etc/init.d/networking restart    重启网络服务
54.231.98.184 github-com.s3.amazonaws.com
```

### 通过Offcloud

网站https://www.offcloud.com，下载。

参考网址：<https://www.cnblogs.com/limitlessun/p/11657960.html>



### 赋予权限

```
$ sudo chmod +x /usr/local/bin/docker-compose
```

### 测试

```
docker-compose -version
```



## 使用

配置docker-compose.yml文件

```yaml
version: '3'
services:
  tomcat:
    restart: always
    image: tomcat
    container_name: myTomcat
    ports:
      - 8080:8080
```

启动[-d 守护态运行]

```
docker-compose up -d
```

查看日志

```
docker-compose logs -f mytomcat
```

删除

```
docker-compose down
```



## 命令说明

### 命令对象与格式

对于 Compose 来说，大部分命令的对象既可以是项目本身，也可以指定为项目中的服务或者容器。如果没有特别的说明，命令对象将是项目，这意味着项目中所有的服务都会受到命令影响。

执行 `docker-compose [COMMAND] --help` 或者 `docker-compose help [COMMAND]` 可以查看具体某个命令的使用格式。

`docker-compose` 命令的基本的使用格式是

```
docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS...]
```

### 命令选项

- `-f, --file FILE` 指定使用的 Compose 模板文件，默认为 `docker-compose.yml`，可以多次指定。
- `-p, --project-name NAME` 指定项目名称，默认将使用所在目录名称作为项目名。
- `--x-networking` 使用 Docker 的可拔插网络后端特性
- `--x-network-driver DRIVER` 指定网络后端的驱动，默认为 `bridge`
- `--verbose` 输出更多调试信息。
- `-v, --version` 打印版本并退出。

### 命令使用说明

`build`

格式为 `docker-compose build [options] [SERVICE...]`。

构建（重新构建）项目中的服务容器。

服务容器一旦构建后，将会带上一个标记名，例如对于 web 项目中的一个 db 容器，可能是 web_db。

可以随时在项目目录下运行 `docker-compose build` 来重新构建服务。

选项包括：

- `--force-rm` 删除构建过程中的临时容器。
- `--no-cache` 构建镜像过程中不使用 cache（这将加长构建过程）。
- `--pull` 始终尝试通过 pull 来获取更新版本的镜像。

`config`

验证 Compose 文件格式是否正确，若正确则显示配置，若格式错误显示错误原因。

### `down`

此命令将会停止 `up` 命令所启动的容器，并移除网络

`exec`

进入指定的容器。

`help`

获得一个命令的帮助。

`images`

列出 Compose 文件中包含的镜像。

`kill`

格式为 `docker-compose kill [options] [SERVICE...]`。

通过发送 `SIGKILL` 信号来强制停止服务容器。

支持通过 `-s` 参数来指定发送的信号，例如通过如下指令发送 `SIGINT` 信号。

```
$ docker-compose kill -s SIGINT
```

`logs`

格式为 `docker-compose logs [options] [SERVICE...]`。

查看服务容器的输出。默认情况下，docker-compose 将对不同的服务输出使用不同的颜色来区分。可以通过 `--no-color` 来关闭颜色。

该命令在调试问题的时候十分有用。

`pause`

格式为 `docker-compose pause [SERVICE...]`。

暂停一个服务容器。

`port`

格式为 `docker-compose port [options] SERVICE PRIVATE_PORT`。

打印某个容器端口所映射的公共端口。

选项：

- `--protocol=proto` 指定端口协议，tcp（默认值）或者 udp。
- `--index=index` 如果同一服务存在多个容器，指定命令对象容器的序号（默认为 1）。

### `ps`

格式为 `docker-compose ps [options] [SERVICE...]`。

列出项目中目前的所有容器。

选项：

- `-q` 只打印容器的 ID 信息。

`pull`

格式为 `docker-compose pull [options] [SERVICE...]`。

拉取服务依赖的镜像。

选项：

- `--ignore-pull-failures` 忽略拉取镜像过程中的错误。

`push`

推送服务依赖的镜像到 Docker 镜像仓库。

`restart`

格式为 `docker-compose restart [options] [SERVICE...]`。

重启项目中的服务。

选项：

- `-t, --timeout TIMEOUT` 指定重启前停止容器的超时（默认为 10 秒）。

`rm`

格式为 `docker-compose rm [options] [SERVICE...]`。

删除所有（停止状态的）服务容器。推荐先执行 `docker-compose stop` 命令来停止容器。

选项：

- `-f, --force` 强制直接删除，包括非停止状态的容器。一般尽量不要使用该选项。
- `-v` 删除容器所挂载的数据卷。

### `run`

格式为 `docker-compose run [options] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND] [ARGS...]`。

在指定服务上执行一个命令。

例如：

```
$ docker-compose run ubuntu ping docker.com
```

将会启动一个 ubuntu 服务容器，并执行 `ping docker.com` 命令。

默认情况下，如果存在关联，则所有关联的服务将会自动被启动，除非这些服务已经在运行中。

该命令类似启动容器后运行指定的命令，相关卷、链接等等都将会按照配置自动创建。

两个不同点：

- 给定命令将会覆盖原有的自动运行命令；
- 不会自动创建端口，以避免冲突。

如果不希望自动启动关联的容器，可以使用 `--no-deps` 选项，例如

```
$ docker-compose run --no-deps web python manage.py shell
```

将不会启动 web 容器所关联的其它容器。

选项：

- `-d` 后台运行容器。
- `--name NAME` 为容器指定一个名字。
- `--entrypoint CMD` 覆盖默认的容器启动指令。
- `-e KEY=VAL` 设置环境变量值，可多次使用选项来设置多个环境变量。
- `-u, --user=""` 指定运行容器的用户名或者 uid。
- `--no-deps` 不自动启动关联的服务容器。
- `--rm` 运行命令后自动删除容器，`d` 模式下将忽略。
- `-p, --publish=[]` 映射容器端口到本地主机。
- `--service-ports` 配置服务端口并映射到本地主机。
- `-T` 不分配伪 tty，意味着依赖 tty 的指令将无法运行。

`scale`

格式为 `docker-compose scale [options] [SERVICE=NUM...]`。

设置指定服务运行的容器个数。

通过 `service=num` 的参数来设置数量。例如：

```
$ docker-compose scale web=3 db=2
```

将启动 3 个容器运行 web 服务，2 个容器运行 db 服务。

一般的，当指定数目多于该服务当前实际运行容器，将新创建并启动容器；反之，将停止容器。

选项：

- `-t, --timeout TIMEOUT` 停止容器时候的超时（默认为 10 秒）。

### `start`

格式为 `docker-compose start [SERVICE...]`。

启动已经存在的服务容器。

### `stop`

格式为 `docker-compose stop [options] [SERVICE...]`。

停止已经处于运行状态的容器，但不删除它。通过 `docker-compose start` 可以再次启动这些容器。

选项：

- `-t, --timeout TIMEOUT` 停止容器时候的超时（默认为 10 秒）。

`top`

查看各个服务容器内运行的进程。

`unpause`

格式为 `docker-compose unpause [SERVICE...]`。

恢复处于暂停状态中的服务。

### `up`

格式为 `docker-compose up [options] [SERVICE...]`。

该命令十分强大，它将尝试自动完成包括构建镜像，（重新）创建服务，启动服务，并关联服务相关容器的一系列操作。

链接的服务都将会被自动启动，除非已经处于运行状态。

可以说，大部分时候都可以直接通过该命令来启动一个项目。

默认情况，`docker-compose up` 启动的容器都在前台，控制台将会同时打印所有容器的输出信息，可以很方便进行调试。

当通过 `Ctrl-C` 停止命令时，所有容器将会停止。

如果使用 `docker-compose up -d`，将会在后台启动并运行所有的容器。一般推荐生产环境下使用该选项。

默认情况，如果服务容器已经存在，`docker-compose up` 将会尝试停止容器，然后重新创建（保持使用 `volumes-from` 挂载的卷），以保证新启动的服务匹配 `docker-compose.yml` 文件的最新内容。如果用户不希望容器被停止并重新创建，可以使用 `docker-compose up --no-recreate`。这样将只会启动处于停止状态的容器，而忽略已经运行的服务。如果用户只想重新部署某个服务，可以使用 `docker-compose up --no-deps -d <SERVICE_NAME>` 来重新创建服务并后台停止旧服务，启动新服务，并不会影响到其所依赖的服务。

选项：

- `-d` 在后台运行服务容器。
- `--no-color` 不使用颜色来区分不同的服务的控制台输出。
- `--no-deps` 不启动服务所链接的容器。
- `--force-recreate` 强制重新创建容器，不能与 `--no-recreate` 同时使用。
- `--no-recreate` 如果容器已经存在了，则不重新创建，不能与 `--force-recreate` 同时使用。
- `--no-build` 不自动构建缺失的服务镜像。
- `-t, --timeout TIMEOUT` 停止容器时候的超时（默认为 10 秒）。

`version`

格式为 `docker-compose version`。

打印版本信息。





## 模板文件

学习网址：http://www.funtl.com/2018/05/13/docker/Docker-Compose-%E6%A8%A1%E6%9D%BF%E6%96%87%E4%BB%B6/



## 实战 Tomcat

```
version: '3.1'
services:
  tomcat:
    restart: always
    image: tomcat
    container_name: tomcat
    ports:
      - 8080:8080
    volumes:
      - /usr/local/docker/tomcat/webapps/test:/usr/local/tomcat/webapps/test
    environment:
      TZ: Asia/Shanghai
```

## 实战 MySQL

### MySQL5

```
version: '3.1'
services:
  mysql:
    restart: always
    image: mysql:5.7.22
    container_name: mysql
    ports:
      - 3306:3306
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
      --max_allowed_packet=128M
      --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
    volumes:
      - mysql-data:/var/lib/mysql

volumes:
  mysql-data:
```

设置

​	设置挂载位置：/var/lib/docker/volumes/***，查看命令docker volume ls;



#### 授权

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'ffffff' WITH GRANT OPTION;
flush privileges;
```

`GRANT 权限 on 数据库名.表名 用户名@登录主机 identified by '用户密码'`;

 `ALL PRIVILEGES`  表示赋给远程登录用户所有权限;

 `*`  代表全部数据库/表;

此处root表示要授权的用户的用户名;

 `@`后面跟具体IP表示只允许该IP访问，如果跟的是  `%`  表示允许所有IP都可以访问;

最后就是在`IDENTIFIED BY`后面设置用户登录的密码。（在连接远程数据库时，需要输入上面设置的用户名和密码）





### MySQL8

```
version: '3.1'
services:
  db:
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```



## 实战wangluo—service

**Dockerfile**

```
FROM java:8 
#VOLUME /tmp 
ADD spring-cloud-config-1.0.0-SNAPSHOT.jar  app.jar 
#RUN bash -c 'touch /app.jar'
#CMD java -jar app.jar
ENTRYPOINT java -jar app.jar
```

**docker-compose.yml**

```yml
version: '3'
services:
 wangluo-service-config:
  build: ./wangluo-service-config/
  #image: wangluo-service-config-image
  container_name: wangluo-service-config
  restart: always
  ports:
   - 8001:8001
  
 wangluo-service-eureka:
  build: ./wangluo-service-eureka/
  #image: wangluo-service-eureka-image
  container_name: wangluo-service-eureka
  restart: always
  ports:
   - 8101:8101  

 wangluo-service-redis:
  image: redis
  container_name: wangluo-service-redis
  restart: always
  ports:
   - 6379:6379
  command: redis-server --requirepass ffffff  --notify-keyspace-events Eglx

  
 wangluo-service-zuul:
  build: ./wangluo-service-zuul/
  #image: wangluo-service-zuul-image
  container_name: wangluo-service-zuul
  restart: always
  ports:
   - 8201:8201
  

```



# 日常排查

## 网址

https://yq.aliyun.com/articles/59144

## 容器性能信息:docker stats





# Docker容器互联

随着 Docker 网络的完善，强烈建议大家将容器加入自定义的 Docker 网络来连接多个容器，而不是使用 `--link` 参数。



### 新建网络

下面先创建一个新的 Docker 网络。

```
$ docker network create -d bridge my-net
```

-d` 参数指定 Docker 网络类型，有 `bridge` `overlay`。其中 `overlay` 网络类型用于 `Swarm mode



### 连接容器

运行一个容器并连接到新建的 `my-net` 网络

```
$ docker run -it --rm --name busybox1 --network my-net busybox sh
```

打开新的终端，再运行一个容器并加入到 `my-net` 网络

```
$ docker run -it --rm --name busybox2 --network my-net busybox sh
```

再打开一个新的终端查看容器信息

```
$ docker container ls

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
b47060aca56b        busybox             "sh"                11 minutes ago      Up 11 minutes                           busybox2
8720575823ec        busybox             "sh"                16 minutes ago      Up 16 minutes                           busybox1
```

下面通过 `ping` 来证明 `busybox1` 容器和 `busybox2` 容器建立了互联关系。