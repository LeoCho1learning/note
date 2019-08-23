# Docker使用

## Docker简介

Docker基于Go语言开发，对线程进行封装隔离，属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其他的隔离的进程，因此也被称为容器。

Docker在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等，简化了容器的创建和维护。Docker技术比虚拟机技术更为轻便、快捷。

传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整的操作系统，在该系统上再运行所需应用进程；容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，也没有硬件虚拟。

## Docker镜像

操作系统分为内核和用户空间。对于Linux，内核启动后，会挂载root文件系统为其提供用户空间支持。例如在官方镜像 ubuntu:18.04 中包含了完整的一套Ubuntu 18.04 最小系统的root文件系统。

Docker镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。

## Docker容器

镜像与容器的关系，就像是类与对象的关系。镜像是静态的定义，容器是镜像运行时的实体。

容器的实质是进程，与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间。

## Docker镜像加速器

由于默认的官方服务器Dockerhub，在国内拉取镜像有时比较慢，这里使用一些国内云服务商提供的国内加速器服务。

在Ubuntu中，添加镜像加速器，在/etc/docker/daemon.json（如果文件不存在，则创建该文件）中按照json的格式添加以下内容：

```json
{
	"registry-mirrors":[
		"https://dockerhub.azk8s.cn",
		"https://reg-mirror.qiniu.com"
	]
}
```

然后重新启动docker服务，

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```

检查加速器是否生效，执行docker info，观察Registry Mirrors中是否已经包含了添加的内容。

## Docker安装

- 首先卸载旧的版本

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

- 更新apt包索引（apt安装，参考 https://docs.docker.com/install/linux/docker-ce/ubuntu/ ）

```shell
sudo apt-get update
```

- 安装包使apt可以use a repository over HTTPS

```shell
sudo apt-get install  apt-transport-https  ca-certificates  curl   gnupg-agent  software-properties-common
```

- 添加docker的official GPG key

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

- 添加稳定库

```shell
sudo add-apt-repository \

   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \

   $(lsb_release -cs) \

   stable"
```

- 安装

```shell
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

- 也可以安装指定版本（也可以安装制定的版本，参考官方链接）
- 也可以通过deb安装包来安装（参考官方链接）

## 升级docker



## docker-ce卸载

```shell
sudo apt-get purge docker-ce
```

删除所有的images，containers，volumes

```shell
sudo rm -rf /var/lib/docker
```

删除之前版本留下的config.json，在这个文件下会存储一些之前登录仓库相关的账户等信息

```shell
sudo rm -r .docker
```



## 验证docker安装

```shell
docker -V           #查看docker版本
systemctl status docker          #查看docker服务状态
sudo systemctl start docker        #若未启动则启动docker服务
sudo docker run hello-world      #测试docker
```

## 免sudo使用docker命令

```shell
sudo groupadd docker             #添加docker用户组
sudo gpasswd -a ${USER} docker       #将用户加入到group内
sudo service docker restart           #重启docker服务
newgrp - docker          #切换当前会话到新group或者重启会话
```

## 使用docker镜像

### 获取镜像

```shell
docker pull [选项] [docker registry 地址[:端口号]/]仓库名[:标签]
```

地址和端口号即docker registry的地址和端口号，例如1x.1x.x.xxx:8888，默认的地址是Dockerhub。

仓库名，用的是两段式名称，即<用户名>/<软件名>，如vision/darknet。对于Dockerhub，如果不给出用户名，则默认为library，也就是官方镜像。

### 运行镜像

拥有镜像之后，可以以该镜像为基础启动运行一个容器。比如我们制作了一个ubuntu:18.04的镜像，我们可以通过如下命令来启动其中的bash：

```shell
docker run -it --rm ubuntu:18.04 bash
```

-it:：-i代表交互式操作，-t代表终端

--rm：表示该容器退出后随之将其删除。

bash：放在镜像后面的是命令，这里我们希望有一个交互式的Shell，因此这里使用了命令Shell

### 列出镜像

```shell
docker image ls
docker images
```

### 虚悬镜像

虚悬镜像值得是既没有镜像名也没有标签的镜像，均为```<none>```，出现的原因可能是，例如一个镜像在官方被更新了，但没有改变镜像名和标签，这样在重新pull的时候，镜像名会转移到新下载的镜像上，而旧的镜像上的名称被取消，从而出现```<none>```。这类无标签的镜像又被成为虚悬镜像。

显示虚悬镜像：

```shell
docker image ls -f dangling=true
```

去除虚悬镜像

```shell
docker image prune
```

### 中间层镜像

为了加速镜像构建、重复利用资源，docker会利用中间层镜像。默认的```docker image ls```只显示顶层镜像，显示包括中间层在内的所有镜像：

```shell
docker image ls -a
```

### 列出部分镜像

根据仓库名列出镜像----```docker image ls ubuntu```

制定仓库名和标签----```docker image ls ubuntu:18.04```

过滤器参数——```--filter  /  -f```

显示镜像摘要：

```shell
docker image ls --digests
```

例如只显示在```mongo:3.2```之后建立的镜像，可以使用命令;

```shell
docker image ls -f since=mongo:3.2
```

### 以特定格式显示(TO_DO)

### 重命名镜像

```shell
docker tag ImageID Repository:TAG
```

### 删除本地镜像

```shell
docker image rm [选项] <image1>[<image2...>]
```

删除镜像可以通过镜像ID，镜像名或者镜像摘要。

### Untagged与deleted

在观察```docker image rm```的删除行为可以发现，删除行为分为两类，Untagged与Deleted。

镜像的唯一标识是其ID和摘要，而一个镜像可以拥有多个标签。删除镜像时，实际上是在删除某个标签下的镜像。于是，会有以下几步：

1. 将满足我们要求的所有镜像的标签取消（Untagged）
2. 由于一个镜像可以对应多个标签，删除了制定标签后，如果还有别的标签指向这个镜像，则不发生Delete行为。只有当一个镜像的所有标签都被取消了。则该镜像失去存在意义，触发删除（Delete）行为。
3. 由于镜像是多层存储结构，同一层可能被不同的镜像所依赖，删除时也是从上层向基础层方向依次进行判断删除，删除到某一层时，需要判断有没有其他镜像依赖于该层，只有当没有任何层依赖该层时，才会真实的删除当前层。
4. 除了镜像的依赖，还需要注意容器对镜像的依赖。在删除镜像前，应该将依赖于该镜像的容器先删除，否则该容器将出现故障。因为容器是以镜像为基础，然后添加容器存储层，组成这样的多层存储结构来运行的。

## 编写DockerFile

### RUN执行命令

RUN指令用于执行命令行命令，有两种格式：

1. shell格式：```RUN <命令>```，与直接在命令行中输入指令相同。
2. exec格式：```RUN ["可执行文件", "参数1", "参数2 "]```，更类似于函数调用

每一个RUN命令都会建立新的一层，过多的```RUN```命令，会使得镜像过于臃肿，不仅增加构建部署的时间，同时也容易出错。Dockerfile支持shell类的行尾添加```\```的命令换行方式 ，以及行首```#```进行注释的格式。

在RUN命令中写完构建的操作过后，要注意添加清理工作的操作命令，删除编译构建的所需要的软件，清理所有下载展开的软件，清理apt缓存文件。因为docker镜像是多层存储，每一层的东西并不会在下一层被删除，而是会一直跟随着镜像。

### 构建镜像

```shell
docker build [选项] <上下文路径/URL/->
```

### 镜像构建上下文（Context）

```docker build```命令最后有一个```.```。如：

```shell
docker build -t nginx:v3 .
```

这里的```.``` 不能简单的认为是当前目录，这里的```.``` 是用以指定上下文目录。

#### 上下文目录（TO_DO）

首先要理解`docker build`的工作原理。

docker在运行时分为：

- Docker引擎（服务端守护进程）
- 客户端工具

docker引擎提供一组REST API， 称为docker remote api， 而如docker命令这样的客户端工具，通过这组api与docker引擎进行交互，从而完成各种功能。但实际上，一切都是使用远程调用形式在服务端（docker引擎）完成。

### 其他```docker build```的用法

#### 直接使用Git repo进行构建

```shell
docker build https://github......
```

#### 用给定的tar压缩包构建

```shell
docker build http://server/context.tar.gz
```

#### 从标准输入中读取Dockerfile进行构建

```shell
docker build - < Dockerfile
或者
cat Dockerfile | docker build -
```

由于是直接从标准输入中读取dockerfile的内容，没有上下文，因此无法将本地文件copy到镜像当中。

#### 从标注输入中读取上下文压缩包进行构建

```shell
docker build - < context.tar.gz
```

如果发现标准输入的文件格式是 gzip 、 bzip2 以及 xz 的话，将会使其为上下文压缩包，直接将其展开，将里面视为上下文，并开始构建。

### COPY复制文件

```shell
COPY [--chown=<user>:<group>] <源路径>... <目标路径>
```

`COPY`指令从构件上写文目录中`<源路径>`的文件/目录中复制到新的一层的镜像内的`<目标路径>`位置。

`<源路径>`可以是多个，甚至可以是通配符，通配符规则要满足的Go的规则。

`<目标路径>`可以是容器内的绝对路径，也可以是相对于工作目录的相对路径，工作目录用`WORKDIR`指定。目标路径不需实现创建。

使用`COPY`命令，源文件的各种元数据将会被保留，如读写等权限，文件变更时间等。

### WORKDIR指定工作目录

```shell
WORKDIR <工作目录路径>
```



### Dockerfile编写实践

```
# ==================================================================
# module list
# ------------------------------------------------------------------
# python        3.6    (apt)
# pytorch       latest (pip)
# mmcv          latest (pip)
# mmdetection
# ==================================================================

# 指定初始镜像
FROM nvidia/cuda:10.0-cudnn7-devel-ubuntu18.04
LABEL maintainer="yucao cyuleo@126.com"

# 调整编码，解决中文显示的问题
ENV LANG C.UTF-8

# --no-install-recommends 避免安装非必须的文件，从而减少镜像的体积
RUN APT_INSTALL="apt-get install -y --no-install-recommends" && \
    PIP_INSTALL="python -m pip --no-cache-dir install --upgrade" && \
    # git只拉取最后10次的commit
    GIT_CLONE="git clone --depth 10" && \

    rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \

    apt-get update && \

# ==================================================================
# tools
# ------------------------------------------------------------------

    DEBIAN_FRONTEND=noninteractive $APT_INSTALL \
        build-essential \
        apt-utils \
        ca-certificates \
        wget \
        git \
        vim \
        && \

    $GIT_CLONE https://github.com/Kitware/CMake ~/cmake && \
    cd ~/cmake && \
    ./bootstrap && \
    make -j"$(nproc)" install && \

# ==================================================================
# python
# ------------------------------------------------------------------

    DEBIAN_FRONTEND=noninteractive $APT_INSTALL \
        software-properties-common \
        && \
    add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL \
        python3.6 \
        python3.6-dev \
        python3-distutils-extra \
        && \
    wget -O ~/get-pip.py \
        https://bootstrap.pypa.io/get-pip.py && \
    python3.6 ~/get-pip.py && \
    ln -s /usr/bin/python3.6 /usr/local/bin/python3 && \
    ln -s /usr/bin/python3.6 /usr/local/bin/python && \
    $PIP_INSTALL \
        setuptools \
        && \
    $PIP_INSTALL \
        numpy \
        scipy \
        pandas \
        cloudpickle \
        scikit-learn \
        matplotlib \
        Cython \
        && \

# ==================================================================
# pytorch
# ------------------------------------------------------------------

    $PIP_INSTALL \
        future \
        numpy \
        protobuf \
        enum34 \
        pyyaml \
        typing \
    	torch \
        && \

    $PIP_INSTALL \
    "git+https://github.com/pytorch/vision.git" && \

    $PIP_INSTALL \
        torch_nightly -f \
        https://download.pytorch.org/whl/nightly/cu100/torch_nightly.html \
        && \

# ==================================================================
# config & cleanup
# ------------------------------------------------------------------

    ldconfig && \
    apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/*

```

## 操作Dockerhub容器

容器是独立运行的一个或一组应用，以及它们的运行态环境。

虚拟机可以理解为模拟运行的一整套操作系统（包括运行态环境和其他系统环境）和跑在上面的应用。

### 启动容器：

启动容器有两种方式：

1. 基于镜像新建一个容器并启动；
2. 将在终止状态（`stopped`）的容器重新启动

#### 新建并启动

命令：`docker run`

```shell
docker run -t -i ubuntu:18.04 /bin/bash
```

`-t`选项让docker分配一个伪终端并绑定到容器的标准输入上，`-i`让容器的标准输入保持打开。

利用`docker run`创建容器时，docker 在后台运行的标准操作包括：（TO_DO）

#### 启动已终止容器

利用`docker container start`，将一个已经终止的容器启动。

#### 后台运行

使用`-d`参数，使docker在后台运行。此时容器不会讲输出的结果打印到宿主机上。

使用`-d`参数启动后会返回一个唯一的id，也可以通过`docker container ls`命令来查看容器信息。

#### 终止容器

使用`docker container stop`来终止一个运行中的容器。

当容器中制定的应用终结时，容器也自动停止。

当用户使用`exit`命令或者`ctrl+d`来退出终端时，所创建的容器立即停止。

终止状态的容器可以用`docker container ls -a`命令看到。

启动一个终止状态的容器：`docker container start`

重启一个容器：`docker container restart`

#### 进入容器

使用`-d`参数时，容器启动后会进入后台。此时想要进入容器进行相关操作，可以使用：

- `docker attach`
- `docker exec`

使用`docker exec`进入伪终端时，输入`exit`不会导致容器的停止，但是`docker attach`仍然会导致容器的停止。

#### 导入和导出容器

导出容器：`docker export`，示例：

```shell
docker export 7691a > ubuntu.tar
```

导入容器快照：`docker import`，从容器快照文件中再导入为镜像，如：

```shell
cat ubuntu.tar | docker import - test/ubuntu:v1.0
```

也可以通过指定URL或者某个目录来导入，如：

```shell
docker import http://example.com/exampleimage.tgz example/imagerepo
```

#### 删除容器

使用`docker container rm`来删除一个处于终止状态的容器。

#### 清理所有终止状态的容器

`docker container prune`

## 常见问题

### Dockerfile apt更换国内源

```shell
RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
RUN apt-get clean
```

### Dockerfile pip更换国内源

创建```pip.conf```文件

```shell
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

复制到Docker中去

```shell
COPY pip.conf /etc/pip.conf
```

