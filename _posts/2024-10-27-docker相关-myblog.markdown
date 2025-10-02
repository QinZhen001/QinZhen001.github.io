---
layout:     post
title:      "docker相关"
date:       2024-10-27 18:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 后端
---

> “Yeah It's on. ”



# docker

对 Docker 最简单并且带有一定错误的认知就是 “Docker 是一种性能非常好的虚拟机”。

正如上面所说，这是有一定错误的说法。Docker 相比于传统虚拟机的技术来说先进了不少，具体表现在 Docker 不是在宿主机上虚拟出一套硬件后再虚拟出一个操作系统，而是让 Docker 容器里面的进程直接运行在宿主机上（Docker 会做文件、网络等的隔离），这样一来 Docker 会 **“体积更轻、跑的更快、同宿主机下可创建的个数更多”。**

docker可以屏蔽环境差异，也就是说，只要你的程序打包到了docker中，那么无论运行在什么环境下程序的行为都是一致的，程序员再也无法施展表演才华了，**不会再有“在我的环境上可以运行”**，真正实现“build once, run everywhere”。

----

* 基于 Linux 内核的 Cgroup，Namespace，以及 Union FS  （docker 使用 Overlay FS）等技术，对进程进行封装隔离，属于操作系统层面的虚拟化技术，由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。（namespace 进行资源隔离，cgroup 进行资源配额）
* 最初实现是基于 LXC，从 0.7 以后开始去除 LXC，转而使用自行开发的 Libcontainer，从 1.11 开始，则进一步演进为使用 runC 和 Containerd。
* Docker 在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护，使得 Docker 技术比虚拟机技术更为轻便、快捷。

为什么要用 Docker? 

* 更高效地利用系统资源
* 更快速的启动时间
* 一致的运行环境
* 持续交付和部署
* 更轻松地迁移
* 更轻松地维护和扩展



## Image/Container/Repository

- **Image：** 和 windows 的那种 iso 镜像相比，Docker 中的镜像是分层的，可复用的，而非简单的一堆文件迭在一起（类似于一个压缩包的源码和一个 git 仓库的区别）。
- **Container：** 容器的存在离不开镜像的支持，他是镜像运行时的一个载体（类似于实例和类的关系）。依托 Docker 的虚拟化技术，给容器创建了独立的端口、进程、文件等“空间”，Container 就是一个与宿机隔离 “容器”。容器可宿主机之间可以进行 port、volumes、network 等的通信。
- **Repository：** Docker 的仓库和 git 的仓库比较相似，拥有仓库名、tag。在本地构建完镜像之后，即可通过仓库进行镜像的分发。常用的 Docker hub 有 https://hub.docker.com/ 、 https://cr.console.aliyun.com/ 等。

---

Docker镜像（Image）和容器（Container）的区别

1. Docker镜像（Image）是一个只读的模板，包含了运行Docker容器所需要的所有文件和依赖。它可以看作是一个容器的静态快照，具有可移植性和版本管理的特性。
2. Docker容器（Container）是基于Docker镜像创建的可运行实体，具有独立的文件系统、运行时环境，以及在容器中运行的进程。容器可以被启动、停止、删除等操作，提供了一种轻量级且隔离的运行环境。

**简而言之，镜像是一种静态的模板，而容器是镜像的运行实例。**从镜像可以创建多个容器，每个容器都是相互独立的，拥有自己的文件系统、网络、进程等。镜像主要用于构建和分发应用程序，而容器用于部署和运行应用程序。



## Daemon

Docker Daemon是**Docker架构中运行在后台的守护进程**，大致可以分为Docker Server、Engine和Job三部分。 Docker Daemon可以认为是通过Docker Server模块接受Docker Client的请求，并在Engine中处理请求，然后根据请求类型，创建出指定的Job并运行



## docker-compose

Docker Compose 文件（通常命名为 `docker-compose.yml`）是用于定义和管理多个 Docker 服务的配置文件。它为应用程序提供了一个清晰、简洁的方式来描述其服务架构、依赖关系、网络和卷等。以下是 Docker Compose 文件的主要作用和特点：

1. 服务定义

Docker Compose 文件允许您定义多个服务，每个服务可以是一个独立的容器。例如，您可以在一个文件中定义一个应用程序的 Web 服务器、数据库服务器和缓存服务等。

```dockerfile
version: '3.8'  # 指定 Compose 文件的版本  
services:       # 定义一个或多个服务  
  web:          # 服务名称  
    image: nginx  # 使用的 Docker 镜像  
    ports:  
      - "8080:80" # 端口映射  
  db:  
    image: postgres # 另一个服务，使用 PostgreSQL 镜像  
    environment:  
      POSTGRES_USER: user  
      POSTGRES_PASSWORD: password  
```

2. 简化管理

通过 `docker-compose up` 命令可以一次性启动所有定义的服务，无需逐个启动每个容器。运行 `docker-compose down` 可以停止并删除所有服务及其网络。

3. 网络和服务之间的连接

Docker Compose 自动为定义的服务创建网络，使它们能够通过服务名称相互通信。例如，`web` 服务可以直接通过名称访问 `db` 服务，而无需使用实际的 IP 地址。

4. 数据持久化

您可以在 Compose 文件中定义数据卷，以确保应用程序的数据在容器重启或更新之间持久化。这样可以避免数据丢失。

```dockerfile
volumes:  
  db_data:  
```

5. 配置环境变量

Docker Compose 文件允许您在服务中轻松配置环境变量，这对于管理应用程序的配置非常重要。

```dockerfile
environment:  
  - NODE_ENV=production  
```

6. 支持多环境配置

通过使用不同的 Compose 文件（例如 `docker-compose.dev.yml` 和 `docker-compose.prod.yml`），可以为不同的环境定义不同的配置，以便在开发、测试和生产环境中灵活操作。

7. 依赖管理

Docker Compose 文件允许您管理服务之间的依赖关系，例如，您可以指定某个服务在另一个服务启动后再启动。使用 `depends_on` 可以设置这种依赖关系。

```
depends_on:  
  - db  
```

8. 重用配置

Compose 文件可以通过 YAML 的扩展功能允许的继承和重用机制，以便重复使用某些服务配置，简化复杂服务的管理。





## Dockerfile

[https://docs.docker.com/engine/reference/builder/#dockerfile-reference](https://docs.docker.com/engine/reference/builder/#dockerfile-reference)

[Packaging your software](https://docs.docker.com/build/building/packaging/)

Docker can build images automatically by reading the instructions from a Dockerfile

Docker可以通过读取Dockerfile中的指令来自动构建镜像

---

和  **`docker-compose`** 的区别：

**Dockerfile**：

* 定义**单个镜像**的构建过程（如安装依赖、复制文件）
* 作用对象：镜像（Image）	

**docker-compose.yml**：

* 编排多容器应用的部署与运行
* 作用对象：容器（Container）及服务间关系

---

两者缺一不可：Dockerfile 解决“镜像怎么做”，Compose 解决“容器怎么跑”。掌握其分工与协作逻辑，是高效管理容器化应用的核心

* Dockerfile 是镜像的“构建蓝图”，专注于单个容器环境的定制化（如安装软件、配置环境）。

* docker-compose.yml 是应用的“编排手册”，专注于多容器协同的部署与管理（如服务依赖、网络配置）。

  



### EXPOSE

Dockerfile 中的 EXPOSE 指令用于声明容器运行时所监听的网络端口，以便让外部的其他容器或主机能够访问该容器的网络服务。

**EXPOSE 指令只是起到文档化的作用，不会自动进行端口映射或将端口暴露给主机，因此在创建容器时，仍需要使用 -p 或 -P(所有暴露的接口) 参数来进行端口映射**

[https://docs.docker.com/guides/docker-concepts/running-containers/publishing-ports/#publishing-all-ports](https://docs.docker.com/guides/docker-concepts/running-containers/publishing-ports/#publishing-all-ports)



### FROM

在 Dockerfile 中，`FROM` 指令的作用是指定基础镜像，这是构建 Docker 镜像的起点。以下是 `FROM` 指令的主要作用和一些相关的要点：

1. 指定基础镜像

- **基础镜像**：`FROM` 指令定义了要使用的基础镜像，例如 `FROM ubuntu:latest` 或 `FROM node:18`。这个镜像可以是官方的也可以是用户自定义的。
- **多阶段构建**：可以在一个 Dockerfile 中使用多次 `FROM` 指令，这称为多阶段构建。每个阶段的镜像可以独立，并且可以在后面的阶段中引用前面的阶段。



### AS

在 Dockerfile 中，`AS` 关键字用于给构建阶段命名，通常与多阶段构建（multi-stage builds）一起使用，它可以帮助你在同一 Dockerfile 中定义多个独立的构建阶段。这样可以提高镜像的构建效率和减少最终镜像的大小。以下是 `AS` 关键字的详细作用：

1. 给构建阶段命名

当使用多阶段构建时，你可以为每个阶段指定一个别名，这样在后续阶段中就可以引用该阶段。通过命名，可以使 Dockerfile 更加清晰，并简化从一个阶段到另一个阶段的复制过程。

2. 简化镜像的构建

在多阶段构建中，使用 `AS` 能够让你选择需要的文件和构建环境，同时避免在最终镜像中包含不必要的开发依赖或构建工具。例如，你可以在一个阶段中构建应用程序，在另一个阶段中仅复制构建结果到一个干净的基础镜像中。

3. 优化镜像大小

通过多阶段构建，你可以只保留最终产品所需的文件，减少最终镜像的大小。这种优化也提高了安全性，因为最终镜像中不会包含构建工具或源代码。

示例

以下是一个使用 `AS` 的多阶段构建的示例：

```
# 第一阶段: 构建应用  
FROM node:18 AS builder  

# 设置工作目录  
WORKDIR /app  

# 复制依赖文件  
COPY package*.json ./  
RUN npm install  

# 复制源代码  
COPY . .  

# 构建应用  
RUN npm run build  

# 第二阶段: 准备最终镜像  
FROM nginx:alpine AS production  

# 将构建的结果复制到 Nginx 的默认目录  
COPY --from=builder /app/dist /usr/share/nginx/html  

# 暴露端口  
EXPOSE 80  

# 启动 Nginx  
CMD ["nginx", "-g", "daemon off;"]  
```

在上面的示例中：

- **第一阶段（builder）**：我们使用了 `FROM node:18 AS builder` 来创建一个名为 `builder` 的构建阶段，其中我们安装依赖并构建应用。
- **第二阶段（production）**：通过 `COPY --from=builder`，我们只复制 builder 阶段中的构建结果 `/app/dist` 到 Nginx 容器中。

### 反斜杠

在 Dockerfile 中，`\`反斜杠的作用是将一行命令拆分成多行。它允许你在多行命令中编写更易于阅读和维护的代码。反斜杠需要放在命令的末尾，以表示下一行是命令的延续，而不是独立的命令。使用反斜杠可以提高 Dockerfile 的可读性，特别是当有较长的命令时。例如：

```dockerfile
RUN apt-get update && \
    apt-get install -y package1 \
    package2 \
    package3
```

在上面的例子中，反斜杠将 apt-get 命令拆分成了三行，使命令更易于阅读和编辑。



### Multi-stage

[https://docs.docker.com/build/building/multi-stage/](https://docs.docker.com/build/building/multi-stage/)

多段构建





## publish 发布包

```
docker login
docker tag <IMAGE_ID> <REPOSITORY>:<TAG>
docker push <REPOSITORY>:<TAG>
```





## dockerignore

```
.dockerignore 文件
```

它类似于.gitignore文件，用于忽略不需要包含在镜像中的文件，从而减少构建上下文的大小并提高构建性能。

---

1. 增加构建性能：Docker在构建镜像时，会将构建上下文的所有文件和目录发送给Docker引擎。如果构建上下文中包含大量的不必要文件和目录，将导致构建时间变长。通过使用Dockerignore文件，可以排除这些不必要的文件和目录，从而减少构建上下文的大小，提高构建性能。
2. 减少镜像大小：构建镜像时，Docker会将构建上下文中的所有文件和目录复制到镜像中。如果构建上下文中包含大量的不必要文件和目录，将导致镜像大小增加。通过使用Dockerignore文件，可以排除这些不必要的文件和目录，从而最小化镜像的大小。
3. 保护敏感信息：Dockerignore文件还可以用于排除包含敏感信息（如密码、秘钥等）的文件和目录，以防止它们被包含在镜像中。



## 例子



### nginx

在docker中启动nginx

```dockerfile
FROM node:20

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

FROM nginx:alpine

COPY --from=0 /app/dist /usr/share/nginx/html

EXPOSE 80

# 前台模式下运行
CMD ["nginx", "-g", "daemon off;"]
```

如果要配置 nginx config

```dockerfile
# 使用官方的 Nginx 镜像  
FROM nginx:latest  

# 复制你的 nginx.conf 到镜像中  
COPY nginx.conf /etc/nginx/nginx.conf  
```





## 命令

[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)



### -idt

"docker -idt" 命令的作用是以交互模式（-i）、分配伪终端（-t）和在后台运行（-d）的方式启动一个 Docker 容器。

具体作用如下：

- 以交互模式运行：在容器中输入命令，并查看命令的输出结果。
- 分配伪终端：分配一个伪终端，使得用户可以与容器进行交互，输入命令和查看命令输出。
- 后台运行：容器进程在后台运行，不会占据当前终端，并允许终端进行其他操作。

综上所述，"docker -idt" 命令可以方便地在 Docker 容器中运行命令，并查看输出结果。

---

举个例子：

```
docker run --name tomcat001 -idt tomcat
```



### --mount

Docker的`--mount`用于将主机上的文件或目录与容器中的文件或目录进行挂载（共享），以实现主机和容器之间的数据共享和持久化存储。

`--mount`选项可以接受多个参数来指定挂载的配置，常用的参数有：

- `type`：指定挂载类型，可以是`bind`（主机文件系统上的目录或文件）、`volume`（Docker卷）、`tmpfs`（临时的内存文件系统）等。
- `source`：指定挂载源，即主机上的目录或文件的路径。
- `target`：指定挂载目标，即容器内部的目录或文件的路径。
- `readonly`：可选参数，指定挂载为只读。
- `volume-opt`：可选参数，用于指定挂载选项，如`volume-opt=type=nfs`表示挂载一个NFS卷。

使用`--mount`选项示例：

```bash
docker run --mount type=bind,source=/host/path,target=/container/path,readonly=true
```

这个命令可以将主机上的`/host/path`目录挂载到容器内的`/container/path`目录，并将挂载设置为只读。

通过使用`--mount`选项，可以更加灵活地控制挂载的方式和选项，以适应不同的应用场景和需求。



### compose

[https://pythondjango.cn/python/tools/3-docker-compose/](https://pythondjango.cn/python/tools/3-docker-compose/)

https://www.cnblogs.com/davis12/p/14312267.html

`docker-compose up`：启动容器。如果当前不存在容器，则会构建镜像并创建容器，并将容器启动起来。`docker-compose up -d`（以后台方式启动容器）

`docker-compose down`：停止并移除容器。会移除已创建的容器以及相关的网络、卷等资源。

`docker-compose start`：启动已创建的容器，不会进行构建镜像和创建容器的操作。

`docker-compose stop`：停止已创建的容器。

`docker-compose restart`：重启已创建的容器。

`docker-compose build`：构建镜像。根据配置的 Dockerfile 构建镜像，并可以指定镜像标签等参数。 (可以加上 --build 参数，Build images before starting containers)

`docker-compose logs`：查看容器日志。

`docker-compose ps`：查看容器的状态。

`docker-compose exec`：在容器中执行命令。

`docker-compose run`：在新容器中运行命令。





### rmi

`docker rmi` 是 Docker 命令行工具中的一个命令，用于删除 Docker 镜像（image）。这个命令在管理 Docker 镜像时非常有用，特别是在你需要释放存储空间或清理不再需要的镜像时。

基本用法

```sh
docker rmi [OPTIONS] IMAGE [IMAGE...]
```

- `IMAGE`：要删除的镜像的名称或 ID。你可以一次删除多个镜像，只需在命令中列出它们的名称或 ID。

常见选项

- `-f` 或 `--force`：强制删除镜像，即使它们正在被使用（例如，存在基于该镜像的容器）。



### run

[https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)

* -it 交互
* -d 后台运行  （守护进程模式）
* -p 端口映射 
* -v 磁盘挂载 

例子：

```bash
docker run -it alpine sh
docker run -d -p 80:80 nginx   
docker run --name=base-container -ti ubuntu   // name: container name
```



### ps

```bash
docker ps 
```

查看进程





### container

```
docker container ls
```

container 相关的命令

docker container commit





### network

[https://docs.docker.com/reference/cli/docker/network/](https://docs.docker.com/reference/cli/docker/network/)

```
docker network
```

docker network create命令用于创建一个Docker网络。Docker网络是一组连接在一起的容器的虚拟网络。使用Docker网络，可以为容器提供独立的网络环境，使其能够相互通信，并与主机或其他网络进行通信。





### init

[https://docs.docker.com/reference/cli/docker/init/](https://docs.docker.com/reference/cli/docker/init/)

Creates Docker-related starter files for your project



### images

```ba
docker images 
```

该命令将会列出你所有本地的镜像，其中包括镜像的名称、标签、镜像 ID、大小等信息。



### inspect

```bash
docker inspect <镜像名称或 ID>
```

docker inspect命令的作用是返回有关Docker对象的详细信息，包括容器、镜像、网络和卷等。

具体来说，docker inspect命令可以显示Docker对象的各种属性，如创建时间、标签、环境变量、网络信息、挂载的卷、容器的配置和状态等。这对于了解Docker对象的内部运行方式和配置非常有用，特别是在调试和故障排除时。



### build

[https://docs.docker.com/build/](https://docs.docker.com/build/)

```bash
// 举个例子

docker build -t cascas321sdo/ddd .
```





#### -t

在 Docker 中，`-t`标志用于为构建的镜像指定一个标签（tag）。标签是一个用于标识和区分不同镜像版本的字符串，通常由“仓库名:标签名”组成。

具体来说，使用`-t`标志可以在构建镜像时为其指定一个易于记忆和识别的标签。例如，`docker build -t myimage:1.0 .`会将构建的镜像命名为`myimage`，并设置其标签为`1.0`。

有了标签，可以更轻松地识别和管理镜像。标签可以用于检索和使用特定版本的镜像，也可以用于发布和共享镜像。



#### --target

`docker build --target` 命令用于在构建Docker镜像时指定要构建的目标阶段（target stage）。

在Dockerfile中，可以使用多个 `FROM` 命令指定多个基础镜像，并且每个基础镜像可以有不同的阶段。可以使用 `--target` 参数来指定构建过程中要使用的特定阶段。这样可以跳过之前的构建步骤，并从指定的阶段开始构建。

使用 `--target` 参数可以加快构建过程，因为不需要重新构建之前的阶段。同时，还可以避免构建不需要的组件或服务，从而减小生成的镜像的大小。

例如，假设Dockerfile的内容如下：

```
FROM base AS build
# 构建阶段1
...

FROM base AS test
# 构建阶段2
...

FROM base AS deploy
# 构建阶段3
...
```

如果要构建并且只关心 `deploy` 阶段，可以使用以下命令：

```
docker build --target=deploy .
```

这样将会跳过构建 `build` 和 `test` 阶段，并仅构建 `deploy` 阶段。





### volume

[https://docs.docker.com/engine/reference/commandline/volume_create/](https://docs.docker.com/engine/reference/commandline/volume_create/)



### kill

杀掉进程

docker kill 973322dc1872

973322dc1872 => CONTAINER ID



### info

该命令将显示与您登录的个人身份相关的详细信息，包括仓库的数量、镜像的数量、运行的容器的数量以及其他相关统计信息。

```
docker info
```


