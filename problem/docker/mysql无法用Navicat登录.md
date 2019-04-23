## mysql无法用Navicat登录
+ 时间：2018.04.23
+ 场景：从docker hub拉取的mysql镜像，启动之后，无法用Navicat连接，报错信息 ```Client does not support authentication protocol requested by server; consider upgrading MySQL client```
+ 原因：未知
+ 解决：
	+ 最先直接使用docker启动的，没有指定用户和密码
	+ 改用docker-compose启动
		+ 指定用户和密码
		+ 指定端口
		+ 指定挂载地址，以保证即使镜像被干掉，数据还在
	```
	version: '2'
    services:
       mysql:
        container_name: mysql
        image: mysql
        ports:
          - "3306:3306"
        environment:
          - MYSQL_USER=root
          - MYSQL_ROOT_PASSWORD=password
        volumes:
          - ./mysql:/var/lib/mysql 
	```
	+ 然后发现，通过 ```docker exec -it ./bin/bash``` 进入容器后，通过 ```mysql -uroot -ppassword```能进入用户，但是Navicat依旧不能连接
	+ 找到【参考文献1】的方案，捣鼓了一下，没解决
	+ 再找到【参考文献2】的方案，其中```@MacielJr```的answer 
		+ ```ALTER USER root IDENTIFIED WITH mysql_native_password BY 'password';```解决
+ Reference: 
1. [解决 --- client does not support authentication](https://blog.csdn.net/u012965373/article/details/81206241)
2. [MySQL 8.0 - Client does not support authentication protocol requested by server; consider upgrading MySQL client](https://stackoverflow.com/questions/50093144/mysql-8-0-client-does-not-support-authentication-protocol-requested-by-server)