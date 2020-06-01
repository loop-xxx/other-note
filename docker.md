# docker

## docker daemon

### 命令

```shell
# 启动docker deamon
systemctl start docker
# 查看docker daemon状态
systemctl status docker
# 停止dokcer deamon
systemctl stop docker
# 重起docker deamon
systemctl restart docker
# 设置开机启动docker deamon
systemctl enable docker
```

## docker 镜像

### 命令

```shell
# 查看镜像:查看本地所有的镜像
docker images
# -q 参数只显示镜像id
docker images -q 
# 搜索镜像:从docker仓库中查找需要的镜像
docker search image_name
#拉取镜像:从docker仓库中下载指定的镜像到本地
# 不指定tag参数将默认下载latest版本
docker pull image_name[:tag]  
# 删除镜像:删除本地镜像
# 根据名称删除本地镜像
docker rmi image_name[:tag]
# 根据id删除本地镜像
docker rmi image_id
```

## docker 容器

### 理解

* docker容器和宿主os共享一个kernel。
* docker容器使用统一文件系统技术, 在宿主机kernel的基础之上叠加整合了一个单独隔离的文件系统, 容器中的进程与宿主机的进程互相隔离(容器/proc目录和宿主机的/proc目录互相隔离 )。
* docker容器具有虚拟网卡, daemon为docker容器提供了虚拟网络 (支持nat模式), docker容器中的网络可以隔离也可以通过nat和宿主os对外共享ip。
* docker容器中的进程对于宿主os来说, 依旧是一个进程, 只是docker容器对运行在容器中的进程进行了的封装,容器中进程使用容器提供的单独隔离的文件系统, 以及虚拟网卡。 
* docker容器主进程运行结束则docker容器退出。

### 命令

```shell
# 创建容器,并在容器运行主进程, 主进程结束则容器退出
docker run [options] image [command [args]] 
# image required 指定容器创建使用的镜像
# command 可选参数,容器创建完成后,启动主进程的命令,不指定则使用image设定的默认命令, 如reids 镜像被加载成容器后, 默认执行启动redis-server的脚本
# option 可选参数, 不指定任何参数,默认为-i
# --name container_name 为容器指定一个容器名,不指定则随机生成 
# -i 以交互式方式运行容器主进程, 容器主进程接受当前控制终端的输入,以及输出/错误输出到当前终端, 如果容器同样指定了-d参数则改为保持stdin打开
# -t 与-i连用,将容器主进程的输入输出, 包装成一个伪终端
# -a 重定向容器主进程的stdin/stdout/stderr
# -d 以守护进程的方式运行容器主进程, 默认关闭stdin/stdout/stderr, 如果指定-i参数则不关闭
```

```shell
# 在指定容器中运行进程
dcoekr exec [options] container command [args]
# container required 指定容器, 容器id或容器名
# command requried 进程的启动命令
# option 可选参数,不指定任何参数,默认为-d
# -i 以交互式方式运行容器进程, 容器进程接受当前控制终端的输入,以及输出/错误输出到当前终端, 如果容器同样指定了-d参数则改为保持stdin打开
# -t 与-i连用,将容器进程的标准输出/输入/错误,包装成一个伪终端
# -a 重定向容器进程的stdin/stdout/stderr
# -d 以守护进程的方式运行容器进程 默认关闭stdin/stdout/stderr, 如果指定-i参数则不关闭
```

```shell
# 查看正在运行的容器
docker ps
# 查看所有容器(包括已退出的容器)
dcoker ps -a
dcoker ps -aq #-q参数只显示id
```

```shell
# 退出正在运行的容器
dcoker stop container

# 如果容器已退出,则重新运行容器主进程
docker start [option]
# -i 将容器主进程的stdin重定向到当前终端,只有start 创建时指定-i参数的容器 时才能使用该参数,否则无效
# -a 重定向容器进程的stdin/stdout/stderr

# 删除容器
docker rm container1 container2 ...
# 查看docker容器/镜像信息
docker inspect container
dcoker inspect image:tag
```

## docker 数据卷

### 数据卷概念

* 宿主机中的目录或文件被docker容器挂载后称为数据卷
* 一个数据卷可以个被多个容器同时关在
* 一个容器也可以挂载多个数据卷

### 数据卷配置

```shell
# 创建容器时, 配置数据卷
# 注意事项
# 目录(文件)必须是绝对路径
# 如果目录不存在,则会自动创建
docker run  [options -v 宿主机目录(文件):容器内目录(文件) -v 宿主机目录(文件):容器内目录(文件) ...]\
image [command [args]]
# 如果不指定宿主机目录, 那么docker将在宿主机的/var/lib/docker/volumes/...下创建一个随机的目录使用
docker run  [options -v 容器内目录(文件) -v 宿主机目录(文件):容器内目录(文件) ...]\
image [command [args]]

# 创建数据卷容器简化数据卷配置
# 创建一个数据卷容器
docker run -it -d -v /root/volume_dir:/volume_dir --name data_volume centos:7
# 创建容器时, 通过数据卷容器配置数据卷
docker run -it -d --volumes-from data_volume --name c1 centos:7
docker run -it -d --volumes-from data_volume --name c2 centos:7
```

## docker 应用部署

### 部署mysql

```shell
docker images
docker search mysql
# hub.docker.com 查看支持的版本号
docker pull mysql:5.7
mkdir /root/mysql
cd /root/mysql
#创建容器
docker run -d \
-p 3306:3306 \
-v $PWD/conf:/etc/mysql/conf.d \
-v $PWD/logs/logs \
-v $PWD/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=toor \
--name mysql \
mysql:5.7
# -p 3306:3306 指定nat端口映射,将容器的3306端口映射到宿主机的3306
# -e 设置容器环境变量, 初始化mysql root@localhost密码
```

### 部署nginx

```shell
docker images
docker search nginx
# hub.docker.com 查看支持的版本号
docker pull nginx # 默认pull latest
mkdir -p /root/nginx/conf
cd /root/nginx
vi conf/nginx.conf # nginx.conf配置文件
events {
  worker_connections  1024;
}
http {
    include       /etc/nginx/mime.types; 
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
# 创建容器
docker run -d \
-p 80:80 \
-v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf \
-v $PWD/logs:/var/log/nginx \
-v $PWD/html:/usr/share/nginx/html \
--name nginx \
nginx
```

### 部署redis

```shell
docker images
docker search redis
# hub.docker.com 查看支持的版本号
docker pull redis:5.0.8
#创建容器
docker run -d \
-p 6379:6379 \
--name redis \
redis:5.0.8
```

## docker镜像创建

### 容器提交为镜像

```shell
# 容器转换为镜像
# 注意事项, 数据卷不会被提交
docker commit container image:tag
# 打包镜像
docker save -o file_path image:tag
# 解包镜像
docker load -i file_path
```

### dockerfile 构建镜像

* dockerfile 编写简单示例

```shell
FROM centos:7
# MAINTAINER 标明作者信息
MAINTAINER loop

# LABEL 用来标明信息的标签, 可以在docker image基本信息中查看

# RUN 镜像构建时执行的命令
RUN yum update -y
RUN yum install -y vim

# COPY build时添加文件到image中, 只能从本地copy
# ADD build时添加文件到image中, 支持从远程服务器拉去, 支持文件解压

# 镜像被加载为容器时,主进程的工作目录
WORKDIR ~/
# 镜像被加载为容器时,启动主进程的命令
CMD ["/bin/bash"]
```

* dockerfile 构建image

```shell
docker build -f ./dockerfile_name -t image:tag dockerfile_path
```