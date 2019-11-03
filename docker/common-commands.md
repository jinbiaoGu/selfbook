# docker common commands


## Version | Info

1. docker version

    > 显示Docker 信息 

2.  docker info  

    > 显示Docker 系统信息,包括镜像和容器数



## 容器操作 


1. docker  ps [OPTIONS]

	> 默认列出所有在运行的容器信息  
	  -a:显示所有容器,包括未运行的  
	  -f:根据条件过滤显示的内容  
      --format:指定返回值的模板文件   
      -l:显示最近创建的容器  
	  -n:列出最近创建的N个容器  
	  -q:静默模式,只显示容器编号  
	  -s:显示总的文件大小  
    >  --no-trunc:不截断输出 


2.  docker inspect [OPTIONS] NAME|ID [NAME|ID...]

	 >  OPTIONS说明:  
	   -f:指定返回值的模板文件
	   -s:显示总的文件大小
	 >  --type:为指定类型返回json


3.  docker top [OPTIONS] CONTAINER [ps OPTIONS]  
	
	>  容器运行时不一定有/bin/bash终端来交互执行top命令，而且容器还不一定有top命令，可以使用docker top来实现查看container中正在运行的进程  
	   例 : docker top mysql  
       查看所有运行容器的进程信息
    >   for i in  `docker ps |grep Up|awk '{print $1}'`;do echo \ &&docker top $i; done

4.  docker attach [OPTIONS] CONTAINER //连接到正在运行中的容器。  
	
	> 要attach上去的容器必须正在运行，可以同时连接上同一个container来共享屏幕（与screen命令的attach类似）  
	  例 : 容器mynginx将访问日志指到标准输出，连接到容器查看访问信息。 
	> docker attach --sig-proxy=false mynginx
		

5.  docker events [OPTINOS] //从服务器获取实时事件

	> options 说明   
	  -f:根据条件过滤事件  
      --since:从指定的时间戳后显示所有事件  
	  --until:流水时间显示到指定的时间为止
	  例1  显示docker 2016年7月1日后的所有事件  
		docker events  --since="1467302400"
	  例2 显示docker 镜像为mysql:5.6 2016年7月1日后的相关事件。
		docker events -f "image"="mysql:5.6" --since="1467302400" 		
    > 也可直接使用日期形式:如--since="2017-07-01"  

6.  docker logs [OPTIONS] CONTAINER//获取容器的日志  
	
	> OPTIONS说明：  

	  -f : 跟踪日志输出  

      --since :显示某个开始时间的所有日志  

      -t : 显示时间戳   

      --tail :仅列出最新N条容器日志  
	  例1  跟踪查看容器mynginx的日志输出。  
		docker logs -f mynginx  
      例2  查看容器mynginx从2016年7月1日后的最新10条日志。  
    >	docker logs --since="2016-07-01" --tail=10 mynginx
	  

7. docker wait [OPTIONS] CONTAINER [CONTAINER...]// 阻塞运行直到容器停止，然后打印出它的退出代码。
    
    > docker wait CONTAINER


8.   docker export [OPTIONS] CONTAINER//将文件系统作为一个tar归档文件导出到STDOUT
	
	> OPTIONS说明：  
	  -o :将输入内容写到文件  
	  例:将id为a404c6c174a2的容器按日期保存为tar文件。  
    >    docker export -o mysql-`date +%Y%m%d`.tar a404c6c174a2

9.   docker port [OPTIONS] CONTAINER [PRIVATE_PORT[/PROTO]]//列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口

	> 例 查看容器mynginx的端口映射情况。
    >    docker port mymysql
  

## 本地镜像管理 


1.  docker images [OPTIONS] [REPOSITORY[:TAG]] 列出本地镜像。


	> -a :列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；

	  --digests :显示镜像的摘要信息；

	  -f :显示满足条件的镜像；

	  --format :指定返回值的模板文件；

      --no-trunc :显示完整的镜像信息；

	> -q :只显示镜像ID。 	

2.  docker rmi [OPTIONS] IMAGE [IMAGE...] //删除本地一个或多少镜像

	>  -f :强制删除；

	>  --no-prune :不移除该镜像的过程镜像，默认移除；  



3.  docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG] 


4.  docker build [OPTIONS] PATH | URL | - // 命令用于使用 Dockerfile 创建镜像。

	  

   >  --build-arg=[] :设置镜像创建时的变量；

      --cpu-shares :设置 cpu 使用权重；

      --cpu-period :限制 CPU CFS周期；

      --cpu-quota :限制 CPU CFS配额；

      --cpuset-cpus :指定使用的CPU id；

      --cpuset-mems :指定使用的内存 id；

      --disable-content-trust :忽略校验，默认开启；

      -f :指定要使用的Dockerfile路径；

      --force-rm :设置镜像过程中删除中间容器；

      --isolation :使用容器隔离技术；

      --label=[] :设置镜像使用的元数据；

      -m :设置内存最大值；

      --memory-swap :设置Swap的最大值为内存+swap，"-1"表示不限swap；

      --no-cache :创建镜像的过程不使用缓存；

      --pull :尝试去更新镜像的新版本；

      --quiet, -q :安静模式，成功后只输出镜像 ID；

      --rm :设置镜像成功后删除中间容器；

      --shm-size :设置/dev/shm的大小，默认值是64M；

      --ulimit :Ulimit配置。

      --tag, -t: 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。

   >  --network: 默认 default。在构建期间设置RUN指令的网络模式
	


5. docker history [OPTIONS] IMAGE //查看指定镜像的创建历史

	>  -H :以可读的格式打印镜像大小和日期，默认为true；

	   --no-trunc :显示完整的提交记录；

	>   -q :仅列出提交记录ID。
 


6. docker save [OPTIONS] IMAGE [IMAGE...] //docker save : 将指定镜像保存成 tar 归档文件。

	    > -o :输出到的文件
		docker 容器导入导出有两种方法：

		一种是使用 save 和 load 命令

		使用例子如下：

		docker save ubuntu:load>/root/ubuntu.tar
		docker load<ubuntu.tar

		一种是使用 export 和 import 命令

		使用例子如下：

		docker export 98ca36> ubuntu.tar
		cat ubuntu.tar | sudo docker import - ubuntu:import
		需要注意两种方法不可混用。


7.  docker load [OPTIONS] //导入使用 docker save 命令导出的镜像。
	> -i :指定导出的文件。

	> -q :精简输出信息。 


8.  docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]//从归档文件中创建镜像
    
    > -c :应用docker 指令创建镜像；

	>  -m :提交时的说明文字；
      
		
   	实例
   	从镜像归档文件my_ubuntu_v3.tar创建镜像，命名为runoob/ubuntu:v4
   	docker import  my_ubuntu_v3.tar runoob/ubuntu:v4  


 - - -




## 容器生命周期管理


1. docker run [OPTIONS] IMAGE [COMMAND] [ARG...] //创建一个新的容器并运行一个命令

	> OPTIONS说明：	

    -a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；

    -d: 后台运行容器，并返回容器ID；

    -i: 以交互模式运行容器，通常与 -t 同时使用；

    -p: 端口映射，格式为：主机(宿主)端口:容器端口

    -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；

    --name="nginx-lb": 为容器指定一个名称；

    --dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；

    --dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；

    -h "mars": 指定容器的hostname；

    -e username="ritchie": 设置环境变量；

    --env-file=[]: 从指定文件读入环境变量；

    --cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；

    -m :设置容器使用内存最大值；

    --net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；

    --link=[]: 添加链接到另一个容器；

    --expose=[]: 开放一个端口或一组端口； 



2.  Docker start/stop/restart 命令
	
	> docker start :启动一个或多个已经被停止的容器

	  docker stop :停止一个运行中的容器

	> docker restart :重启容器


3.  docker kill [OPTIONS] CONTAINER [CONTAINER...]//杀掉一个运行中的容器
	
	> OPTIONS说明：  -s :向容器发送一个信号

	
4.  docker rm [OPTIONS] CONTAINER [CONTAINER...]//删除一个或多少容器
	
	OPTIONS说明：

    -f :通过SIGKILL信号强制删除一个运行中的容器

    -l :移除容器间的网络连接，而非容器本身

    -v :-v 删除与容器关联的卷


5.  Docker pause/unpause 

	> docker pause [OPTIONS] CONTAINER [CONTAINER...]//暂停容器中所有的进程
	> docker unpause [OPTIONS] CONTAINER [CONTAINER...]//恢复容器中所有的进程。


6. docker create [OPTIONS] IMAGE [COMMAND] [ARG...]//创建一个新的容器但不启动它

	> 语法同 docker run


7. docker exec [OPTIONS] CONTAINER COMMAND [ARG...]//在运行的容器中执行命令

	> OPTIONS说明：

	    -d :分离模式: 在后台运行

    	-i :即使没有附加也保持STDIN 打开

	    -t :分配一个伪终端



## 镜像仓库



1.  docker serach  

     > **docker search [options] term**  
          OPTIONS 说明:  
          --automated:只列出automated build类型的镜像  
          -s:列出收藏数不小于指定值的镜像(-s 40 ,列出收藏数不小于40的镜像)  
     > --no-trunc:显示完整的镜像描述    

2.  docker pull [OPTIONS] NAME[:TAG|@DIGEST]//从镜像仓库中拉取或者更新指定镜像

	> OPTIONS说明：

       -a :拉取所有 tagged 镜像

	>  --disable-content-trust :忽略镜像的校验,默认开启
	

3.  docker login 

	> 实例

	登陆到Docker Hub

	docker login -u 用户名 -p 密码

	登出Docker Hub

	docker logout


4. docker push [OPTIONS] NAME[:TAG] //将本地的镜像上传到镜像仓库,要先登陆到镜像仓库

	> OPTIONS说明：

	    --disable-content-trust :忽略镜像的校验,默认开启
		上传本地镜像myapache:v1到镜像仓库中。
		docker push myapache:v1 


## 容器rootfs命令

1.  docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]] //从容器创建一个新的镜像。
		
	OPTIONS说明：

    -a :提交的镜像作者；

    -c :使用Dockerfile指令来创建镜像；

    -m :提交时的说明文字；

    -p :在commit时，将容器暂停。

2.  docker cp :用于容器与主机之间的数据拷贝

	> OPTIONS说明：

	    -L :保持源目标中的链接

		将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下。

		docker cp /www/runoob 96f7f14e99ab:/www/

		将主机/www/runoob目录拷贝到容器96f7f14e99ab中，目录重命名为www。

		docker cp /www/runoob 96f7f14e99ab:/www

		将容器96f7f14e99ab的/www目录拷贝到主机的/tmp目录中。

		docker cp  96f7f14e99ab:/www /tmp/



3. docker diff [OPTIONS] CONTAINER //检查容器里文件结构的更改。

			
	实例

	查看容器mymysql的文件结构更改。

	runoob@runoob:~$ docker diff mymysql
	A /logs
	A /mysql_data
	C /run
	C /run/mysqld
	A /run/mysqld/mysqld.pid
	A /run/mysqld/mysqld.sock
	C /tmp	
