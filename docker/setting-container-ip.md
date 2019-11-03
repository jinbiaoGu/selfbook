# 设置 docker 容器(container)的 固定 ip 地址

1. Docker 安装后，会默认创建下面三种风络类型

$docker network ls

```
	NETWORK        NAME           DRIVER            SCOPE
	
	989488896ae     bridge          bridge            local
	
	254853164ee     host 			host 			  local 
	
	fef5qw5f464e    none 			null              local

```
    
启动Dockerr的时候，用 --network 参数，可以指定网络类型  

例：

· docker run -itd --name test1 --network bridge --ip 172.17.0.10 centos:latest /bin/bash ·

*bridge:桥接网络*   

`默认情况下启动的docker container，都是使用bridge ,docker 安装时创建的桥接网络，每次docker container重启时，会按照顺序获取对应的IP地址，这个就导致重启下，Docker 的IP地址就变了`  

*none:无指定网络*    

·使用 --network=none, docker container 就不会分配局域网的IP·  

*host:主机网络*

·使用 --network=host，此时，Docker 容器的网络会附属在主机上，两者是互通的。
例如，在容器中运行一个Web服务，监听8080端口，则主机的8080端口就会自动映射到容器中。 ·



2 .创建自定义网络：（设置固定IP）
启动Docker容器的时候，使用默认的网络是不支持指派固定IP的，如下
```
docker run -itd --net bridge --ip 172.17.0.10 centos:latest /bin/bash
6eb1f228cf308d1c60db30093c126acbfd0cb21d76cb448c678bab0f1a7c0df6
docker: Error response from daemon: User specified IP address is supported on user defined networks only.
``` 
+ 步骤1: 创建自定义网络
```
docker network create --subnet=172.18.0.0/16 mynetwork
➜ ~ docker network ls
NETWORK ID     NAME        DRIVER       SCOPE
9781b1f585ae    bridge       bridge       local
1252da701e55    host        host        local
4f11ae9c85de    mynetwork      bridge       local
237ea3d5cfbf    none        null        local
```
+ 步骤2: 创建Docker容器
` docker run -itd --name networkTest1 --net mynetwork --ip 172.18.0.2 centos:latest /bin/bash `