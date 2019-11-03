# 如何获取 docker 容器(container)的 ip 地址


1. 进入容器内部后

	` cat /etc/hosts`

2. 使用命令

	```
	docker inspect --format '{{.NetworkSettings.IPAddress }}' <container-ID>

	或者

	docker inspect <container-ID>

	或者

	docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name or container_id


	```

3. 可以考虑在~/.bashrc 中写一个bash函数

	```
	function docker_ip() {

		sudo docker inspect --format '{{.NetwordSttings.IPAddress}}' $1
	}

	source ~/.bashrc
	
	$docker_ip <container-ID>

	```

4. 要获取所有容器名称及其IP地址只需一个命令。

`
docker inspect -f '{{.Name}} - {{.NetworkSettings.IPAddress }}' $(docker ps -aq)
`

如果使用docker-compose命令将是： 

`
docker inspect -f '{{.Name}} - {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)
`

5. 显示所有容器IP地址：
`
docker inspect --format='{{.Name}} - {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)
`
--------------------- 
