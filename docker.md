-p -v  前面是主机 后面是docker

#电影
docker run -d -p 8096:8096 -v /jellyfin/config:/config -v /mp4:/media jellyfin/jellyfin
#aria2下载
docker run -d \
  --name aria2-pro \
  --restart unless-stopped \
  --log-opt max-size=1m \
  -e PUID=$UID \
  -e PGID=$GID \
  -e UMASK_SET=022 \
  -e RPC_SECRET=846525202 \
  -e RPC_PORT=6800 \
  -p 6800:6800 \
  -e LISTEN_PORT=6888 \
  -p 6888:6888 \
  -p 6888:6888/udp \
  -v $PWD/mp4/aria2-config:/config \
  -v $PWD/mp4/aria2-downloads:/downloads \
  p3terx/aria2-pro

#内网穿透

  docker run --restart=always --network host -d -v /etc/frp1/frpc.ini:/etc/frp/frpc.ini --name frpc snowdreamtech/frpc:0.27.0


# 网站导航
sudo docker run -d \
  --name=heimdall \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/London \
  -p 90:80 \
  -p 443:443 \
  -v ~/heimdall/config:/config \
  -v ~/heimdall/lang:/var/www/localhost/heimdall/resources/lang \
  --restart unless-stopped \
  lscr.io/linuxserver/heimdall

# ftp服务
  docker run -d -v $(pwd)/data:/home/ding -e USER=ding -p 4567:21 -p 3000-3010:3000-3010 dingchunq/pyftp

  docker run -d -v /srv/dev-disk-by-uuid-a61e873c-4534-4678-b6c0-f7b847a8969f/bt:/home/ding -e USER=ding -e PASSWORD=123456 -p 4567:21 -p 3000-3010:3000-3010 --name ftps dingchunq/pyftp:arm

  docker run -d -v /root/data:/home/ding -e USER=ding -e PASSWORD=123456 -p 4567:21 -p 3000-3010:3000-3010 --name ftps pyftp:arm64

# Dockerfile创建镜像
docker build -t uname:latest .
#push到dockerhub
 命令行登录
 docker login -u uname
 上传
 docker push name/uname:latest

1、Docker容器信息
##查看docker容器版本
docker version
##查看docker容器信息
docker info
##查看docker容器帮助
docker --help
2、镜像操作
提示：对于镜像的操作可使用镜像名、镜像长ID和短ID。

2.1、镜像查看
##列出本地images
docker images
##含中间映像层
docker images -a


##只显示镜像ID
docker images -q
##含中间映像层
docker images -qa


##显示镜像摘要信息(DIGEST列)
docker images --digests
##显示镜像完整信息
docker images --no-trunc


##显示指定镜像的历史创建；参数：-H 镜像大小和日期，默认为true；--no-trunc  显示完整的提交记录；-q  仅列出提交记录ID
docker history -H redis
2.2、镜像搜索
##搜索仓库MySQL镜像
docker search mysql
## --filter=stars=600：只显示 starts>=600 的镜像
docker search --filter=stars=600 mysql
## --no-trunc 显示镜像完整 DESCRIPTION 描述
docker search --no-trunc mysql
## --automated ：只列出 AUTOMATED=OK 的镜像
docker search  --automated mysql


2.3、镜像下载
##下载Redis官方最新镜像，相当于：docker pull redis:latest
docker pull redis
##下载仓库所有Redis镜像
docker pull -a redis
##下载私人仓库镜像
docker pull bitnami/redis


2.4、镜像删除
##单个镜像删除，相当于：docker rmi redis:latest
docker rmi redis
##强制删除(针对基于镜像有运行的容器进程)
docker rmi -f redis
##多个镜像删除，不同镜像间以空格间隔
docker rmi -f redis tomcat nginx
##删除本地全部镜像
docker rmi -f $(docker images -q)
2.5、镜像构建
##（1）编写dockerfile
cd /docker/dockerfile
vim mycentos
##（2）构建docker镜像
docker build -f /docker/dockerfile/mycentos -t mycentos:1.1
3、容器操作
提示：对于容器的操作可使用CONTAINER ID 或 NAMES。

3.1、容器启动
##新建并启动容器，参数：-i  以交互模式运行容器；-t  为容器重新分配一个伪输入终端；--name  为容器指定一个名称
docker run -i -t --name mycentos
##后台启动容器，参数：-d  已守护方式启动容器
docker run -d mycentos
注意：此时使用"docker ps -a"会发现容器已经退出。这是docker的机制：要使Docker容器后台运行，就必须有一个前台进程。解决方案：将你要运行的程序以前台进程的形式运行。

##启动一个或多个已经被停止的容器
docker start redis
##重启容器
docker restart redis
3.2、容器进程
##top支持 ps 命令参数，格式：docker top [OPTIONS] CONTAINER [ps OPTIONS]
##列出redis容器中运行进程
docker top redis
##查看所有运行容器的进程信息
for i in  `docker ps |grep Up|awk '{print $1}'`;do echo \ &&docker top $i; done
3.3、容器日志
##查看redis容器日志，默认参数
docker logs rabbitmq
##查看redis容器日志，参数：-f  跟踪日志输出；-t   显示时间戳；--tail  仅列出最新N条容器日志；
docker logs -f -t --tail=20 redis
##查看容器redis从2019年05月21日后的最新10条日志。
docker logs --since="2019-05-21" --tail=10 redis
3.4、容器的进入与退出
##使用run方式在创建时进入
docker run -it centos /bin/bash
##关闭容器并退出
exit
##仅退出容器，不关闭
快捷键：Ctrl + P + Q
##直接进入centos 容器启动命令的终端，不会启动新进程，多个attach连接共享容器屏幕，参数：--sig-proxy=false  确保CTRL-D或CTRL-C不会关闭容器
docker attach --sig-proxy=false centos
##在 centos 容器中打开新的交互模式终端，可以启动新进程，参数：-i  即使没有附加也保持STDIN 打开；-t  分配一个伪终端
docker exec -i -t  centos /bin/bash
##以交互模式在容器中执行命令，结果返回到当前终端屏幕
docker exec -i -t centos ls -l /tmp
##以分离模式在容器中执行命令，程序后台运行，结果不会反馈到当前终端
docker exec -d centos  touch cache.txt
3.5、查看容器
##查看正在运行的容器
docker ps
##查看正在运行的容器的ID
docker ps -q
##查看正在运行+历史运行过的容器
docker ps -a
##显示运行容器总文件大小
docker ps -s



##显示最近创建容器
docker ps -l
##显示最近创建的3个容器
docker ps -n 3
##不截断输出
docker ps --no-trunc


##获取镜像redis的元信息
docker inspect redis
##获取正在运行的容器redis的 IP
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' redis
3.6、容器的停止与删除
##停止一个运行中的容器
docker stop redis
##杀掉一个运行中的容器
docker kill redis
##删除一个已停止的容器
docker rm redis
##删除一个运行中的容器
docker rm -f redis
##删除多个容器
docker rm -f $(docker ps -a -q)
docker ps -a -q | xargs docker rm
## -l 移除容器间的网络连接，连接名为 db
docker rm -l db
## -v 删除容器，并删除容器挂载的数据卷
docker rm -v redis
3.7、生成镜像
##基于当前redis容器创建一个新的镜像；参数：-a 提交的镜像作者；-c 使用Dockerfile指令来创建镜像；-m :提交时的说明文字；-p :在commit时，将容器暂停
docker commit -a="DeepInThought" -m="my redis" [redis容器ID]  myredis:v1.1
3.8、容器与主机间的数据拷贝
##将rabbitmq容器中的文件copy至本地路径
docker cp rabbitmq:/[container_path] [local_path]
##将主机文件copy至rabbitmq容器
docker cp [local_path] rabbitmq:/[container_path]/
##将主机文件copy至rabbitmq容器，目录重命名为[container_path]（注意与非重命名copy的区别）
docker cp [local_path] rabbitmq:/[container_path]




