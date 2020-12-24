![img](https://up.sowevo.com/img/20201224162029.png)

# Docker概述

## Docker为什么会出现?

环境配置的难题:开发环境,测试环境,模拟环境

开发者常常会说:它在我的机器可以跑了(It works on my machine),言下之意就是,其他机器很可能跑不了.

安装的时候,可不可以把原始环境一模一样地复制过来.

**Docker**针对上面的问题,提出了解决方案

通过隔离机制将服务器压榨到极致

## Docker的历史

2010年,几个年轻人开了家公司`dotCloud`

做一些pass的云计算技术,容器的技术

他们的技术命名为Docker

后来干不下去了...要倒闭

2013年Docker就开源了,就火了

2014年4月9日Docker1.0发布了

为什么这么火🔥?十分的轻巧!

容器技术之前,都是用的VMware虚拟机,十分笨重

Docker容器,也是一种虚拟机

> Docker是基于Go语言开发的

官网https://www.docker.com/

文档https://docs.docker.com/

仓库https://hub.docker.com/

## Docker能干啥!

- 虚拟机技术缺点:
  1. 资源占用多
  2. 冗余步骤多
  3. 启动慢

- 容器技术
  - 容器化技术模拟的不是一台完整的电脑
- Docke与虚拟机技术的不同
  - 传统虚拟机:虚拟一堆硬件,运行一个完整的系统,然后再这个系统上安装和运行软件
  - Docker容器:直接使用宿主机的内核,容器没有自己的内核
  - 每个容器间是相互隔离的,没个容器都都有一个自己的文件系统

> DevOps(开发,运维)

**应用更快的交付和部署**

传统:一堆帮助文档,安装程序

Docker:打包镜像,发布测试,一键运行

**更便捷的升级和扩缩容**

使用了Docker后,项目部署就像搭积木

项目打包为一个镜像,直接在服务器A,服务器B上部署

**更简单的系统运维**

容器化之后,开发,测试,环境高度一致

**更高效的计算资源利用**

Docker是内核级的虚拟化,可以在一个物理机上运行很多实例!服务器的性能被压榨到极致!

# Docker 的安装

## Docker的基本组成

![img](https://up.sowevo.com/img/20201224173423.png)

- 镜像(Image):
  - Docker镜像相当于一个模板,可以通过这个模板来创建容器,镜像==>run==>容器,通过一个镜像可以常见多个容器
- 容器(container)
  - Docker利用容器技术,独立运行一个或者一组应用,通过镜像来创建的
  - 启动,停止,删除等命令
  - 可以理解为一个建议的linux系统
- 仓库(repository)
  - 仓库就是存放镜像的地方
  - 仓库分为公有仓库和私有仓库
  - 默认是国外的仓库,可以配置为国内的镜像

## 安装

- 安装

  ```sh
  $ curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
  ```

  安装完成后，Docker 服务将会自动启动。输入下面的命令来验证：

  ```shell
  $ sudo systemctl status docker
  ```

  查看安装的Docker的版本

  ```shell
  $ docker version  
  ```

  ![image-20201224175422903](https://up.sowevo.com/img/20201224175422.png)

- Zsh插件的使用

  1. 确认安装了Zsh

  2. 修改`~/.zshrc`文件

  3. 找到插件配置项`plugins=()`，增加`docker docker-compose`两个插件

     ![image-20201224180153473](https://up.sowevo.com/img/20201224180153.png)

  4. `source ~/.zshrc`

- 使用

  - 运行hello-world

    ```shell
    $ docker run hello-world
    ```

    ![image-20201224175629771](https://up.sowevo.com/img/20201224175629.png)

  - 查看安装的镜像

    ```shell
    $ docker images
    REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
    hello-world   latest    bf756fb1ae65   11 months ago   13.3kB
    ```

## 容器加速镜像

```shell
$ sudo mkdir -p /etc/docker
$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://ro9g7idi.mirror.aliyuncs.com"]
}
EOF
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker

# 腾讯云可以用这个   https://mirror.ccs.tencentyun.com 地址
```

## 底层原理

### Docker是怎么工作的

Docker是一个Client-Server(C/S)结构的系统,Docker的守护进程运行在主机上,通过Socket等方式从客户端访问

![img](https://up.sowevo.com/img/20201224201858)

Server收到Client的命令,就会执行这个命令

默认我们安装的Docker包含了服务端与客户端,所以我们在同一台服务商的操作也是经过客户端==>服务端这个过程的

我们可以在本机安装一个客户端==>远程连接远程服务器上的docker服务端

### Docker为什么比VM块?

1. Docker比虚拟机有着更少的抽象层

2. Docker利用的是宿主机的内核,VM是需要Guest OS

   ![img](https://up.sowevo.com/img/20201224202439.jpg)

​    新建一个容器的时候,Docker不需要像VM一样重新加载一个操作系统内核,避免引导

# Docker的常用命令

## 帮助命令

```shell
$ docker version  # 显示docker的版本
$ docker info     # 显示docker的系统信息
$ docker help     # 帮助命令
```

## 镜像命令

- **dokcer images**: 查看本地主机上的镜像

```shell
$ dokcer images   # 查看本地主机上的镜像
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    bf756fb1ae65   11 months ago   13.3kB
# 选项:
#  -a, --all             显示所有镜像,（默认隐藏中间镜像）
#  -f, --filter filter   根据提供的条件过滤输出
#  -q, --quiet           仅显示ID
```

- **docker search**:搜索镜像

```shell
$ docker search nginx
NAME                               DESCRIPTION                                    STARS     OFFICIAL   AUTOMATED
nginx                              Official build of Nginx.                       14198     [OK]       
jwilder/nginx-proxy                Automated Nginx reverse proxy for docker con…  1932                 [OK]              
# 选项:
#  -f, --filter filter   根据过滤条件进行过滤
#      --limit int       一页显示多少条
```

- **docker pull**:下载镜像

```shell
# 可以指定docker 镜像的版本 
# docker pull mysql:5.7.9
# 如果不写tag,默认就是latest
$ docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
6ec7b7d162b2: Pull complete 
fedd960d3481: Pull complete 
7ab947313861: Pull complete 
64f92f19e638: Pull complete 
3e80b17bff96: Pull complete 
014e976799f9: Pull complete 
59ae84fee1b3: Pull complete 
ffe10de703ea: Pull complete 
657af6d90c83: Pull complete 
98bfb480322c: Pull complete 
6aa3859c4789: Pull complete 
1ed875d851ef: Pull complete 
Digest: sha256:78800e6d3f1b230e35275145e657b82c3fb02a27b2d8e76aac2f5e90c1c30873
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest
```

- **docker rmi**:删除镜像

```shell
# 两种形式
# docker rmi 镜像id 
# docker rmi 镜像名:版本号 
$ docker rmi a347a5928046
$ docker rmi hello-world:latest 
```

## 容器命令

- **docker run**:新建容器并启动

  ```shell
  $ docker run [可选参数] images
  # 参数说明
  --name="name"   容器名称   tomcat01,mysql3306,用来区分容器
  -d							后台方式运行
  -p							指定容器的端口
        -p ip:主机端口:容器端口
  			-p 主机端口:容器端口(常用的)
  			-p 容器端口
  -p             指定随机端口			
  -i             以交互模式运行容器,通常与 -t 同时使用
  -t             为容器重新分配一个伪输入终端,通常与 -i 同时使用
  --rm           用完就删除,一般用来测试
  ```

  测试启动容器

  ```shell
  # 启动centos容器,基础版本,很多命令都是不完善的
  $ docker run -it  centos /bin/bash
  ```

- **docker ps**列出所有正在运行的容器

  ```shell
  $ docker ps
  # 参数说明
  -a          显示所有容器,包括没有在运行的容器
  -n=2        显示返回条数
  -q          只显示容器的编号
  ```

- **docker  rm**删除容器

  ```shell
  $ docker rm 容器id    # 删除指定的容器
  # 参数说明
  -f     强制删除
  ```

- 启动和停止容器

  ```shell
  $ docker start   容器id   # 启动容器
  $ docker stop    容器id    # 停止容器
  $ docker restart 容器id # 重启容器
  $ docker kill    容器id    # 强制停止容器
  ```

## 常用其他命令

- **docker stats** 查看容器使用资源情况

  ```shell
  # 返回容器资源使用情况,一秒刷新一次
  $ docker stats
  # 返回容器资源使用情况,不刷新
  $ docker stats --no-stream
  # 返回容器资源使用情况,已经停掉的也显示
  $ docker stats -a
  # 返回指定容器资源使用情况
  $ docker stats 容器id
  ```

- 后台启动容器**-d**

  ```shell
  # -d 后台启动
  $ docker run -d centos
  ```

- **docker logs**查看日志

  ```shell
  $ docker logs -tf --tail 10 容器id
  # docker logs [OPTIONS] CONTAINER
  # OPTIONS说明：
  -f       跟踪日志输出
  -t       显示时间戳
  --since  显示某个开始时间的所有日志
  --tail   仅列出最新N条容器日志
  ```

- **docker top**查看容器中的进程信息

  ```shell
  # docker top 容器id
  $ docker top 919121a5a1a6
  ```

- **docker inspect** 查看容器的元数据

  ```shell
  # docker inspect 容器id
  $ docker inspect 919121a5a1a6
  ```

- 进入当前正在运行的容器

  - 通常都是后台运行的,需要进入容器,修改配置之类的
  
  - 方式一**docker exec**
  
    ```bash
    # 命令
    # docker exec -it 容器id /bin/bash
    $ docker exec -it 919121a5a1a6 /bin/bash
    ```
  
  - 方式二**docker attach**
  
      ```shell
      # 命令
      # docker attach 容器id
      $ docker attach 919121a5a1a6
      ```
  
  - 两种方式的区别
    - docker exec     在容器中启动新的终端
    - docker attach  进入容器正在执行命令的那个终端.

- **docker ps**从容器拷贝文件到主机上

  ```shell
  # docker cp 容器id:容器内的文件名 宿主机上的文件名
  $ docker cp 919121a5a1a6:/root/123456.txt /root
  
  
  # docker cp 宿主机上的文件名 容器id:容器内的文件名
  $ docker cp /root/123456.txt 919121a5a1a6:/root
  ```

## 案例

### nginx

```shell
# docker run -d -p 1234:80 --name nginx-1234 nginx
# -d 后台运行
# -p 端口映射
# --name nginx-1234 容器的名称
$ docker run -d -p 1234:80 --name nginx-1234 nginx
```

### tomcat

```shell
# docker run -it -p 8080:8080 --name tomcat8080 tomcat
# -it 交互式启动
# -p 端口映射
# --name tomcat8080 容器的名称
$ docker run -it -p 8080:8080 --name tomcat8080 tomcat
```

### elasticsearch

```shell
# docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.10.1
# -d  后台运行
# -p  端口映射,有两个
# -e  镜像的环境参数 具体由镜像来指定,看下镜像的文档
# --name elasticsearch 容器的名称
$ docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e "ES_JAVA_OPTS=-Xms64m -Xmx512m" elasticsearch:7.10.1
```

## 总结

![img](https://up.sowevo.com/img/20201224224200.png)

# 可视化

## portainer

- 安装

  ```shell
  $ docker run -d -p 8000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer 
  ```

- 用的比较少

## Rancher





