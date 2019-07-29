title: 个人常用的一些docker启动命令
author: hiyo 桔世
date: 2019-07-24 09:02:55
tags:
---
## 服务器中docker常用的命令与操作



### 重启

```shell
docker restart nginx
```

#### 进入docker

```shell
docker exec -it hexo bash
```

大部分容器内没有安装vim 
```shell
$ apt-get update
$ apt-get install vim
```

### 退出docker

```shell
exit
```
<!-- more -->
### docker 状态
```shell
docker stats
```

### hexo docker 启动
```shell
docker run --name=elfop-hexo -e HEXO_SERVER_PORT=4000 -v /usr/elfop\ Folders/elfop-hexo:/app -p 4000:4000 spurin/hexo
```
### docker hexo重启
```shell
docker restart elfop-hexo
```
复制docker中文件到本地
```shell
docker container cp hexo:/opt/hexo/ipple1986/_config.yml .
```

https://spurin.com/2019/03/10/Creating-a-Blog-with-Hexo-Docker-and-Github-Free-Hosting-and-https/

## docker mysql
```shell
启动 docker run --name mysql --restart=always -e MYSQL_ROOT_PASSWORD=123456 -d -p 3306:3306 mysql:tag 
```

## docker zabbix
### 拉取zabbix多个功能项
```shell
docker pull zabbix/zabbix-server-mysql
docker pull zabbix/zabbix-agent
docker pull zabbix/zabbix-java-gateway
docker pull zabbix/zabbix-web-apache-mysql
```

```shell
docker run --name zabbix-server -t \
      -e DB_SERVER_HOST="mysql" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix" \
      -e MYSQL_ROOT_PASSWORD="123456" \
      --network default-net \
      -p 10051:10051 \
      --restart always \
      -d zabbix/zabbix-server-mysql:latest
```

```shell
docker run --name zabbix-web -t \
      -e DB_SERVER_HOST="mysql" \
      -e ZBX_SERVER_HOST="zabbix-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix" \
      -e MYSQL_ROOT_PASSWORD="123456" \
      -p 8080:80 \
      --restart=always \
      --network=default-net \
      -d zabbix/zabbix-web-apache-mysql:latest
```

```shell
zabbix-server本机安装agent
docker run --name zabbix-agent \
      -p 10050:10050 \
      -e ZBX_HOSTNAME="beijing-ali" \
      -e ZBX_SERVER_HOST="127.0.0.1" \
      -e ZBX_SERVER_PORT=10051 \
      --network=default-net \
      --restart=always \
      -d zabbix/zabbix-agent:latest
```
```shell
被监控的主机agent
docker run --name zabbix-agent \
      -p 10050:10050 \
      -e ZBX_HOSTNAME="xianggang-ali" \
      -e ZBX_SERVER_HOST="127.0.0.1" \
      -e ZBX_SERVER_PORT=10051 \
      --restart=always \
      -d zabbix/zabbix-agent:latest

```

## docker network 
解决网桥链接多容器之间的通信问题
容器内同一network互相通信,而与外界隔离

```shell
链接已运行容器与网桥
docker network connect default-net mysql
```

## 更新docker的配置
docker update --restart=always dockerid


## docker fastDFS
运行fastDFS镜像
```shell

docker run --name fastdfs \
    -v /data/fastdfs_data:/data \ 
    -p 8081:8080 \
    --restart=always \
    -d \
    -e GO_FASTDFS_DIR=/data fastdfs 

```

## 查看docker的ip

### 根据容器name获取ip
```shell
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mysql
```

### 查看所有容器ip
```shell
docker inspect --format='{{.Name}} - {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)
```

## docker redis

### 运行redis
```shell
    docker run --name redis \
    --network default-net \
    -p 6379:6379 \
    --restart=always \
    -v /data/redis/dir:/data \
    -d redis:latest \
    redis-server --appendonly yes \
    --requirepass "123456"
```



## nigex  docker启动
```shell
docker container run --name nginx -v "$PWD/html":/usr/share/nginx/html -v "$PWD/conf":/etc/nginx -p 80:80 -d nginx
```