# Docker

> Docker是一个开源应用容器引擎。
>
> Docker支持将软件编译成一个镜像，然后再镜像中各个软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像；
>
> 运行中的这个镜像称为容器，启动速度很快。

## 核心概念

> Docker主机（Host）：安装了Docker程序的机器（直接安装在操作系统之上）
>
> Docker客户端：连接eiDocker主机进行操作
>
> Docker API：与Dokcer的守护进程进行通信
>
> Docker 仓库：Docker仓库用来保存`镜像`，可以理解为代码控制中的代码仓库
>
> Docker镜像：软件打包好的镜像

## 使用Docker步骤

1. 安装Docker
2. 去Docker仓库找到对应的镜像
3. 使用Docker运行这个镜像，这个镜像就会生成一个Docker容器
4. 容器的启动或停止就是对软件的启动或停止

## docker镜像制作

> 基于GitHub项目

1.克隆GitHub文件

```
docker run --name repo alpine/git clone https://github.com/docker/getting--started/git docker cp repo:/git/getting-started
```

2.创建镜像

```
cd getting-started docker build -t docker101tutorial
```

3.运行镜像

```
docker run -d -p 80:80  --name docker-toturial docker101tutorial
```

## docker安装

### linux

1. `uname -r`查看当前centos 版本
2. `yum upodate`更新yum
3. 进入root权限用户输入`yum install docker`
4. `systemctl start docker`启动docker
5. 设置为开机启动`systemctl enable docker`
6. 停止docker`systemctl stop docker`

## 常用操作

### 镜像操作

1. 搜索镜像`docker search mysql`,去`docker hub网站`搜索镜像
2. 拉去镜像`docker pull mysql`(输入镜像名即可)
3. 查看所有镜像：`docker images`
4. 删除指定镜像：`docker rmi image-id`。eg：docker rmi d435eee2caa5
5. 进入容器内部：`docker exec -it name /bin/bash`

### 容器操作

软件镜像->运行镜像->产生一个容器

1. 安装容器：`docker run --name mytomcat -d tomcat -p 8080:8080`
   - \-- name：给容器起名字
   - \- d  ：后台运行
   - \- p：端口映射,`服务器端口：容器端口`:服务器端口映射到容器端口
   - \-  v : 文件目录映射/目录挂载，外部文件目录映射tomcat/nginx等文件目录
   - tomcat ：Image id
2. 查看运行中的容器：`docker ps`
3. 停止容器：`docker stop CONTAINER-ID`,eg:`docker stop f6c65ce4e3dc `
4. 查看所有容器：`docker ps -a`
5. 启动容器：`docker start CONTAINER-ID`
6. 删除指定容器：`docker rm CONTAINER-ID `
7. 查看容器的日志：`docker logs container-name/container-id`
8. 重启容器：`systemctl restart docker`

### 环境搭建

> 如果在服务器上安装必须先把外部的mysql关闭，防止端口冲突

1. 创建启动mysql

   `docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=146325 -d mysql `

2. 创建启动redis

   `docker run -d --name myredis -p 6379:6379 redis --requirepass "redis_cache_146325"`

3. 创建启动rabbitmq消息队列

   `docker run -d -p 5672:5672 -p 15672:15672 --name RabbitMQ image-id`

4. 创建启动nginx

   ```powershell
   > docker run -d --name=nginx -p 80:80 -v /data/nginx/www:/usr/share/nginx/html -v /data/nginx/conf:/etc/nginx nginx
   ## 将工程和配置文件映射
   ```

5. 创建启动tomcat

   ```powershell
   > docker pull tomcat
   > docker run -di --name=tomcat -p 8080:8080 -v /usr/local/tomcat/webapps:/usr/local/tomcat/webapps tomcat
   #  -v /usr/local/tomcat/webapps/servel.xml:/usr/local/tomcat/conf/servel.xml 、有点问题
   ## 将工程和配置文件映射
   ```

6. 创建启动elasticsearch

   ```powershell
   > docker search elasticsearch
   > docker pull  docker pull docker.io/elasticsearch:6.5.0
   > docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name ELS elasticsearch
   # 设置默认启动占用内存256M，9300端口为分布式节点端口
   ```

### 部署工程到tomcat 

1. 将项目文件上传到容器中的tomcat目录

   创建容器时可以进行目录挂载`-v`。

   ```shell
   > cp *.war  /usr/local/tomcat/webapps # 复制war到指定目录
   > mv *.war  /usr/local/tomcat/webapps # 剪贴war到指定目录
   ```

2. 重启tomcat

   ```shell
   > docker restart tomcat
   ```

3. 查看tomcat的内部

   ```shell
   > docker exec -it tomcat /bin/bash
   ```

### 上传本地工程部署到nginx

1. 将项目文件上传到容器中的nginx目录

2. 进入容器、找到root目录

   ```powershell
   > docker exec -it nginx /bin/bash
   ```

3. 将所有静态资源拷贝到root目录下

   1. 重命名`上传的文件夹名称`改为html

      ```shell
      > mv dist html
      ```

     2. 将html目录拷贝到root目录

        ```shell
        > docker cp html nginx:/usr/share/nginx/
        > docker cp nginx.conf nginx:/etc/nginx/ 
        ```

### Docker Compose

> **Docker Compose 可以轻松、高效的管理容器，它是一个用于定义和运行多容器 Docker 的应用程序工具**

## docker 部署spring boot

在pom文件中添加properties、插件

  ```xml
    <properties>
        <docker.image.prefix>spring-boot-demo</docker.image.prefix>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <!--插件-->
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>1.0.0</version>
                <configuration>
                    <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
                    <dockerDirectory>src/main/docker</dockerDirectory>
                    <resources>
                        <resource>
                            <targetPath>/</targetPath>
                            <directory>${project.build.directory}</directory>
                            <include>${project.build.finalName}.jar</include>
                        </resource>
                    </resources>
                </configuration>
            </plugin>
            
        </plugins>
    </build>
  ```

使用maven打包成jar文件、创建 `Dockerfile`文件，将打包好的jar和`Dockerfile`放在同一目录下，`Dockerfile`加入配置

```dockerfile
FROM java:8
VOLUME /tmp
ADD hotel-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

将文件上传至服务器或虚拟机（装有docker），进入目录

```powershell
> docker build -t 项目名称 .
```

构建完成后

```powershell
> docker run -d -p 8080:8080 项目名称 
> docker ps -a 
# 查看当前运行的镜像
```







































