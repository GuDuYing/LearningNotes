# 安装Mysql

1. 环境：centos、docker

2. 安装过程：

        选择的镜像源为：mysql/mysql-server。
        1. 拉取镜像
        docker pull mysql/mysql-server
        2. 新建一个my.cnf文件 和 data 文件夹
        my.cnf 文件内容为：
```
        [mysqld]
        user=mysql
        character-set-server=utf8
        [client]
        default-character-set=utf8
        [mysql]
        default-character-set=utf8
```

        3.新建并运行一个mysql容器

```
        docker run -d -p 3306:3306 --restart always -e MYSQL_ROOT_PASSWORD=123456  --name mysql1  --mount type=bind,src=$PWD/docker/mysql/config/my.cnf,dst=/etc/my.cnf --mount type=bind,src=$PWD/docker/mysql/data,dst=/var/lib/mysql mysql/mysql-server
```

+ -d : 成功启动容器后输出容器的完整ID 
+ -p : 端口映射，此处映射主机3306端口到容器mysql1的3306端口
+ --restart always : 告诉docker，这个容器要自动启动
+ -e : 设置容器的环境变量。-e MYSQL_ROOT_PASSWORD=123456就代表mysql的root的密码是123456
+ --name : 容器名称
+ --mount : 资料卷挂载（外部挂载）      
        
使用 docker ps 查看是否启动,状态为 healthy 时 才算成功。
若不成功，可通过以下命令查看日志
```
docker logs mysql1
```
若成功，通过以下命令进入容器
```
docker exec -it mysql01 bash
```
使用以下命令进入mysql
```
mysql -u root -p
```
输入密码后，若输入正确的密码，依然报错，可尝试输入
```
mysql
```
若能进去后，请改root的密码
```
ALTER user 'root'@'localhost' IDENTIFIED BY 'pwd123456';
```

切换到mysql数据库。
```
use mysql
```
查看用户信息
```
select user,host from user;
```
考虑到root只能在本地才能链接，所以新建一个新用户
```
mysql> CREATE USER 'Test'@'localhost' IDENTIFIED BY 'pwd123456';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'Test'@'localhost' WITH GRANT OPTION;
mysql> CREATE USER 'Test'@'%' IDENTIFIED BY 'pwd123456';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'Test'@'%' WITH GRANT OPTION;
```

退出mysql
```
exit
```
退出容器
```
exit
```

使用tomcat工具进行链接测试是否成功。

若提示1251错误，可以进行进入mysql，输入以下命令
```
mysql> ALTER USER 'Test'@'%' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
mysql> ALTER USER 'Test'@'%' IDENTIFIED WITH mysql_native_password BY '12345678';
mysql> FLUSH PRIVILEGES;
```



