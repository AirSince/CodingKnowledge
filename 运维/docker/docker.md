# Docker笔记

参考[Docker入门实践](https://yeasy.gitbook.io/docker_practice/)学习

## 基本概念
### 镜像
操作系统分为内核和用户空间。对于 Linux 而言，内核启动后，会挂载root文件系统为其提供用户空间支持。而Docker镜像（Image），就相当于是一个root文件系统。比如官方镜像ubuntu:18.04就包含了完整的一套Ubuntu 18.04最小系统的root文件系统。

Docker 镜像 是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像 不包含 任何动态数据，其内容在构建之后也不会被改变
#### 分层存储
在 Docker 设计时，利用 Union FS 的技术，将其设计为分层存储的架构。镜像不是像一个ISO那样的打包文件，镜像只是一个虚拟的概念，其实际体现并非由一个文件组成，而是由一组文件系统组成，或者说，由多层文件系统联合组成。
分层存储的特征还使得镜像的复用、定制变的更为容易。甚至可以用之前构建好的镜像作为基础层，然后进一步添加新的层，以定制自己所需的内容，构建新的镜像。
### 容器
镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的 类 和 实例 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
### 仓库
集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。

### 常用命令
启动容器
``` shell
$ docker run -it --rm ubuntu:18.04 bash
```
* -it：这是两个参数，-i：交互式操作，-t 终端。可进入bash操做终端。
* --rm：这个参数是容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用--rm可以避免浪费空间。
* ubuntu:18.04：用ubuntu:18.04镜像为基础来启动容器，也可使用镜像ID。
* bash：放在镜像名后的是命令，选择Shell，因此用的是 bash。

以交互方式进入docker容器内部
```shell
$ docker exec -it webserver bash
```

退出
```shell
$ exit
```

停止Docker并删除
```shell
$ docker stop webserver
$ docker rm webserver
```

获取Docker镜像
```shell
$ docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```
* Docker 镜像仓库地址：地址的格式一般是 <域名/IP>[:端口号]。默认地址是 Docker Hub(docker.io)。
* 仓库名：如之前所说，这里的仓库名是两段式名称，即 <用户名>/<软件名>。对于 Docker Hub，如果不给出用户名，则默认为 library，也就是官方镜像。

查看本地镜像
```shell
$ docker image ls
$ docker images
```

查看镜像、容器、数据卷所占用的空间
```shell
$ docker system df
```



### Dockerfile

Dockerfile 中每一个指令都会建立一层。Union FS 有最大层数限制的，比如AUFS，曾经是最大不得超过 42 层，现在是不得超过 127 层。
Dockerfile 支持 Shell 类的行尾添加 \ 的命令换行方式，以及行首 # 进行注释的格式。
```shell
FROM debian:stretch

RUN set -x; buildDeps='gcc libc6-dev make wget' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```
注意：每一层构建的最后一定要清理掉无关文件，避免制作出很臃肿的镜像。

构建镜像

在 Dockerfile 文件所在目录执行：
```shell
$ docker build [选项] <上下文路径/URL/->
```
例如
```shell
$ docker build -t nginx:v3 .
```
-t 指定最终镜像名称
. 表示当前目录（用户会指定构建镜像上下文的路径）

将 Dockerfile 置于一个空目录下，或者项目根目录下。如果该目录下没有所需文件，那么应该把所需文件复制一份过来。如果目录下有些东西确实不希望构建时传给 Docker 引擎，那么可以用 .gitignore 一样的语法写一个 .dockerignore，该文件是用于剔除不需要作为上下文传递给 Docker 引擎的。


FROM 指定基础镜像

除了选择现有镜像为基础镜像外，Docker 还存在一个特殊的镜像，名为 scratch。这个镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像。
如果以scratch为基础镜像的话，意味着你不以任何镜像为基础，接下来所写的指令将作为镜像第一层开始存在。
```
FROM scratch
```

RUN 执行命令

shell格式：类似直接在命令行中输入命令。
```shell
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html

```

exec格式：RUN ["可执行文件", "参数1", "参数2"]，类似函数调用格式。

COPY 复制文件
COPY 指令将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置。
```shell
COPY package.json /usr/src/app/
```
<源路径> 可以是多个，甚至可以是通配符，其通配符规则要满足 Go 的 filepath.Match 规则，如：
```shell
COPY hom* /mydir/
COPY hom?.txt /mydir/
```
<目标路径> 可以是容器内的绝对路径，也可以是相对于工作目录的相对路径（工作目录可以用 WORKDIR 指令来指定）。
使用 COPY 指令，源文件的各种元数据都会保留。比如读、写、执行权限、文件变更时间等。这个特性对于镜像定制很有用。特别是构建相关文件都在使用 Git 进行管理的时候。
在使用该指令的时候还可以加上 --chown=<user>:<group> 选项来改变文件的所属用户及所属组。
```shell
COPY --chown=55:mygroup files* /mydir/
COPY --chown=bin files* /mydir/
COPY --chown=1 files* /mydir/
COPY --chown=10:11 files* /mydir/
```