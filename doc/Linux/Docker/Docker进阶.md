![img](https://up.sowevo.com/img/20201224162029.png)

# Docker Compose

## 简介

Docker

DockerFile build run 手动操作,单个容器!

微服务,服务多,相互依赖

使用Docker Compose来高效管理,定义多个容器

- 官方介绍

  定义运行多个容器

  使用yaml配置文件

  所有环境都可以使用

==就是批量容器编排==

## 安装

```shell
# 要去git上确认下最新的版本
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
# 加上权限
$ sudo chmod +x /usr/local/bin/docker-compose
```

## 初体验!

官方的例子

1. 创建文件夹

   ```shell
   $ mkdir composetest
   $ cd composetest
   ```

2. 新建一个`app.py`文件

   ```python
   import time
   
   import redis
   from flask import Flask
   
   app = Flask(__name__)
   cache = redis.Redis(host='redis', port=6379)
   
   def get_hit_count():
       retries = 5
       while True:
           try:
               return cache.incr('hits')
           except redis.exceptions.ConnectionError as exc:
               if retries == 0:
                   raise exc
               retries -= 1
               time.sleep(0.5)
   
   @app.route('/')
   def hello():
       count = get_hit_count()
       return 'Hello World! I have been seen {} times.\n'.format(count)
   ```

3. 新建一个`requirements.txt`文件

   ```
   flask
   redis
   ```

4. 创建`Dockerfile`文件

   ```dockerfile
   FROM python:3.7-alpine       # 从Python3.7映像开始构建
   WORKDIR /code                # 将工作目录设置为/code
   ENV FLASK_APP=app.py         # 设置flask使用的环境变量
   ENV FLASK_RUN_HOST=0.0.0.0   
   RUN apk add --no-cache gcc musl-dev linux-headers # 安装gcc和其他依赖项
   COPY requirements.txt requirements.txt
   RUN pip install -r requirements.txt               # 使用requirements安装依赖
   EXPOSE 5000                                       # 监听5000端口
   COPY . .
   CMD ["flask", "run"]                              # 执行flask命令
   ```

5. 创建`docker-compose.yml`文件

   ```yaml
   version: "3.9"
   services:
     web:
       build: .
       ports:
         - "5000:5000"
     redis:
       image: "redis:alpine"
   ```

6. 使用compose构建并运行你的app

   ```shell
   $ docker-compose up
   ```

7. 使用compose停止你的app

   ```shell
   $ docker-compose down
   ```

## 都干了些什么

1. 根据Dockerfile构建镜像,下载镜像
2. 容器名称的的处理
3. 新建一个网络给这个`项目`使用

## 配置文件的编写

**[官方文档链接](https://docs.docker.com/compose/compose-file/compose-file-v3/#compose-file-structure-and-examples)**

瞅瞅开源项目的`docker-compose.yml`文件

```yaml
version: "3.9"           # 1.compose版本
#======================================    
services:                # 2.服务
  web:                   #   服务名
    build: .             #   服务构建目录,会在这个目录下找Dockerfile开始构建
    ports:               #   端口映射
      - "5000:5000" 
    depends_on:          #   多个服务依赖的情况,被依赖的会先启动
      - redis            #   web依赖redis
    environment:         #   一些环境变量
      RACK_ENV: development
      SHOW: 'true'  
  redis:
    image: "redis:alpine" # 指定一个镜像
    expose:              # 暴露一些端口
      - "3000"
      - "8000"
#======================================
networks:               # 3.其他配置
  frontend:
  backend:
volumes:
  db-data:    
```

## 再体验-WordPress

部署WordPress

1. 建个文件夹

   ```sh
   $ cd my_wordpress/
   ```

2. 编写`docker-compose.yml`文件

   ```yaml
   version: '3.3'
   
   services:
      db:
        image: mysql:5.7
        volumes:
          - db_data:/var/lib/mysql
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: somewordpress
          MYSQL_DATABASE: wordpress
          MYSQL_USER: wordpress
          MYSQL_PASSWORD: wordpress
   
      wordpress:
        depends_on:
          - db
        image: wordpress:latest
        ports:
          - "8000:80"
        restart: always
        environment:
          WORDPRESS_DB_HOST: db:3306
          WORDPRESS_DB_USER: wordpress
          WORDPRESS_DB_PASSWORD: wordpress
          WORDPRESS_DB_NAME: wordpress
   volumes:
       db_data: {}
   ```

3. 启动项目

   ```shell
   # 后台那运行
   $ docker-compose up -d
   ```

## 再再体验-SpringBoot

1. SpringBoot写个项目

2. 编写Dockerfile

   ```dockerfile
   FROM java:8
   COPY ./target/*.jar /app.jar
   EXPOSE 8080
   CMD ["--server.port=8080"]
   ENTRYPOINT ["java","-jar","/app.jar"]
   ```

3. 编写`docker-compose.yml`文件

   ```yaml
   version: "3.9"
   services:
     spring-boot:
       build: .
       ports:
       - "8080:8080"
       depends_on:
         - redis
     redis:
       image: redis
   ```

4. 启动

   ```shell
   # 后台运行
   $ docker-compose up -d
   ```

   

