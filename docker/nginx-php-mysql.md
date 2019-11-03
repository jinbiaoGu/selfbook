# nginx ,php,mysql 的docker 基础配置教程

### 创建本地资源文件夹
//本人的本地资源配置在/var下,执行下以命令

`mkdir -p /var/data/{mysql-data,mysql-conf,php,www,nginx,log} `

``` 
	/data		
	   /mysql-data 数据库备份
	   /mysql-conf 数据库配置
	   /php  php配置文件
	   /www	网站源代码
	   /nginx  nginx 配置
	   /log    日志文件	
``` 

### 拉取镜像

```
	docker pull nginx 
	docker pull php:7.1-fpm
	docker pull mysql

```

### 运行mysql容器

```
	docker run \
	-p 3306:3306 \
	--name my-mysql \
	-v /var/data/mysql-data:/var/lib/mysql \
	-v /var/data/mysql-conf/my.cnf:/etc/mysql/my.cnf \
	-e  MYSQL_ROOT_PASSWORD=root \
	-v /etc/localtime:/etc/localtime:ro \
	-d mysql

```
>	参数说明  

	-d 让容器在后台运行  

	-v 从主机挂目录或者单个文件到容器中
	
	-p 添加主机到容器的端口映射，前面是映射到本地的端口，后面是需要映射的端口  

	-e 设置环境变量，MYSQL_ROOT_PASSWORD这里是设置mysql的root用户的初始密码  

	--name 容器的名字，随便取，但是必须唯一  


*注意事项* 

	如果不想映射到主机的端口，只暴露端口给容器内调用，可以使用–expose。
	例: docker run --expose 80  ubuntu bash 


> 进入mysql 容器

	`docker exec -it my-mysql /bin/bash`
	
> 开启mysql远程连接 

```
	mysql -uroot -proot
	user mysql
	GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
	FLUSH PRIVILEGES;
```
> 重启mysql
`service mysqld restart `




### 运行php容器

```
 docker container run \
 -p 9000:9000 \
 --name my-php-fpm \
 -v /var/data/www:/usr/share/nginx/html \
 -v /var/data/php:/usr/local/etc/php \
-v /var/data/log:/phplogs
 -d php:7.1-fpm

```

> 进入php容器内:
`docker exec -it my-php-fpm`
	在/usr/local/etc/php的文件中没有发现php.ini配置文件.这个问题好像是使用php-fpm会遇到的.网上找到解决方法:
	//https://serverfault.com/questions/840046/why-is-there-no-php-ini-file-when-i-install-php-in-a-docker-container

> 解决没有php.ini配置文件的问题

 1. docker cp my-php-fpm:/usr/src/php.tar.xz ./
 2. 解压php.tar.xz,然后将php.ini-development 或者 php.ini-production 改为php.ini
 3. docker cp php.ini:/usr/local/etc/php/php.ini

> 安装php扩展

` docker exec -it my-php-fpm /bin/bash `

```
apt-get update
apt-get install libpng-dev libjpeg-dev libfreetype6-dev freetype* libjpeg*
docker-php-ext-install gd
docker-php-ext-enable gd
docker-php-ext-install mysqli

```

> 在次运行php容器


```
 docker container run \
 -p 9000:9000 \
 --name my-php-fpm \
 -v /var/data/www:/usr/share/nginx/html \
 -v /var/data/php:/usr/local/etc/php \
 -d php:7.1-fpm

```
*注释事项* 
```
也可以使用--link ,让容器之间安全的进行交互
--link 参数的格式为 --link name:alias，其中 name 是要链接的容器的名称，alias 是这个连接的别名。
这样在容器中就可以用别名代替ip地址 

docker-php-ext-install pdo pdo_mysql
如果报  /usr/local/bin/docker-php-ext-enable: cannot create /usr/local/etc/php/conf.d/docker-php-ext-pdo_mysql.ini: Directory nonexistent    
解决方案：
   直接在/usr/local/etc/php目录下面新建 conf.d目录和对应的docker-php-ext-pdo_msql.ini文件
其中docker-php-ext-pdo_msql.ini的内容为：
extension=pdo_mysql.so

``` 

### 运行nginx 

1.我们先运行无挂载的容器,拿到nginx的一些配置文件

```
docker run -P -d  --name n_nginx nginx

```

2. 然后使用docker cp 操作复制出文件

```
  1. docker cp n_nginx:/etc/nginx.conf /var/data/nginx
  2. docker cp n_nginx:/etc/nginx/conf.d /var/data/nginx/conf.d
```

3. 删除这前运行的Nginx容器

```
 docker stop n_nginx
 docker rm n_nginx

```

4. 重新运行nginx容器 

```
    # docker run \
      -d \
      -p 80:80 \
      -v /var/data/www:/usr/share/nginx/html \
      -v /var/data/nginx/nginx.conf:/etc/nginx/nginx.conf:ro \
      -v /var/data/nginx/conf:/etc/nginx/conf.d \
      -v /var/data/logs/var/log/nginx \
      --link m_phpfpm:phpfpm \
      --name m_nginx nginx

```


> 参数说明:

	-d 让容器在后台运行  

	-p 添加主机到容器的端口映射  

	-v 添加目录映射,这里最好nginx容器的根目录最好写成和php容器中根目录一样。但是不一点非要一模一样,如果不一样在配置nginx的时候需要注意  

	-–name 容器的名字  

	–-link 与另外一个容器建立起联系  


> 编辑配置文件


1.修改/var/data/nginx/conf.d/default.conf


```
     server {
        listen       80;
        server_name  _;
        #charset koi8-r;
        access_log  /var/log/nginx/default_nginx.log  main;
        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm index.php;
        }
        #error_page  404              /404.html;
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}
        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
            root           /usr/share/nginx/html;
            fastcgi_pass   phpfpm:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

```


*注意事项* 

``` 
配置文件中的fastcgi_pass phpfpm:9000;，因为我们在启动容器的时候--link了phpfpm容器，所以这里直接填phpfpm，就能找到phpfpm的IP，当然你也可以不配置--link，那么你可以这样找到容器IP，再将IP填入。

```


