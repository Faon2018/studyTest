



# 基于docker的mysql主从架构搭建

## mysql 主从架构概念及运用场景

### MySQL Replication

主从复制（也称 AB 复制）允许将来自一个MySQL数据库服务器（主服务器）的数据复制到一个或多个MySQL数据库服务器（从服务器）。

原理图：

![image-20201221152519219](..\images\image-20201221152519219.png)

当主库数据发生改变时，会把改变写入到二级制日志文件（binary log），从库的io线程会读取主库的二进制日志到从库的relay log(中继文件，也是二进制)，

从库的数据库线程会检查relay log文件是否有更新，当有更新时读取并执行。

## binary log 三种模式

+ STATEMENT

  ```reStructuredText
  每修改一条sql就会记录到binlog中。
  优点： 仅仅记录了SQL，不会记录执行结果，减少了binlog日志量，节约了IO，提高性能
  缺点: 会出现结果不一致情况,系统函数或系统变量。如select now()、insert into @@hostname
  ```

+ ROW

  ```reStructuredText
  每条记录的修改都会被记录。
  优点： 不存在数据不一致情况
  缺点: 一旦修改alter table会出现binlog暴涨。
  ```

+ MIXED

  ```reStructuredText
  以上两种模式混合使用。一般的复制使用STATEMENT模式保存binlog。
  ```

  

### 一主多从

如果一主多从的话，这时主库既要负责写又要负责为几个从库提供二进制日志。此时可以稍做调整，将二进制日志只给某一从，这一从再开启二进制日志并将自己的二进制日志再发给其它从。或者是干脆这个从不记录只负责将二进制日志转发给其它从，这样架构起来性能可能要好得多，而且数据之间的延时应该也稍微要好一些。工作原理图如下：

![image-20201221155126104](..\images\image-20201221155126104.png)

### 搭建步骤

+ 1.创建并启动主库容器

  ```shell
  docker run --name mysql_master -p 3306:3306  -d -e MYSQL_ROOT_PASSWORD=123456  mysql:5.7
  ```

+ 2.进入主库容器

  ```shell
  docker exec -it mysql_master
  ```

+ 3.编辑  **/etc/mysql/my.cnf**  文件

  ```shell
  cd /etc/mysql
  #安装vim
  apt-get update
  apt-get install vim
  vim my.cnf
  ```

  ```she
  [mysqld]
  ## 同一局域网内注意要唯一
  server-id=1 
  ## 开启二进制日志功能，可以随便取（关键）
  log-bin=mysql-bin
  ```

+ 4.由于前三步，在容器中下载安装vim较慢，所以挂载比较快

  ```sh
  #先在系统中 创建/home/mysql/mysql_master.cnf 文件,并编辑写入第三步的配置信息
  docker run --name mysql_master -p 3306:3306  -d -v /home/mysql/mysql_master.cnf:/etc/mysql/my.cnf -e MYSQL_ROOT_PASSWORD=123456  mysql:5.7
  ```

  

+ 5.重启服务

  ```shell
  ##重启mysql服务使配置生效
  service mysql restart
  ##重启容器
  docker start mysql_master
  ```

  

+ 6.Master数据库中创建数据同步用户 **slave**

  ```mysql
  #创建slave用户
  CREATE USER 'slave'@'%' IDENTIFIED BY '123456';
  #授予REPLICATION SLAVE, REPLICATION CLIENT权限
  GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'%';
  ```

  

+ 7.创建并启动从库容器，同主库相同，挂载配置文件如下：

  ```she
  docker run --name mysql_master -p 3307:3306  -d -v /home/mysql/mysql_slave_01.cnf:/etc/mysql/my.cnf -e MYSQL_ROOT_PASSWORD=123456  mysql:5.7
  ```

  

  ```shell
  
  [mysqld]
  ## 设置server_id,注意要唯一
  server-id=2  
  ## 开启二进制日志功能，以备Slave作为其它Slave的Master时使用（本次搭建架构中将第一个从库开启二进制日志文件作为其他从库的同步源）
  log-bin=mysql-slave-bin   
  ## relay_log配置中继日志
  #relay_log=edu-mysql-relay-bin  
  ```

  

  

+ 8.一样的重启服务即可

+ 9.主从库数据库配置

  ```mysql
  ##进入主数据库
  show master status;
  ```

  ![image-20201221172315940](..\images\image-20201221172315940.png)

```mysql
##进入从数据库
CHANGE MASTER TO master_host = '172.17.0.2', ##主库ip(可以是主库所在容器的ip,也可以是容器所在服务器ip)
 master_user = 'slave',##前面在主库创建的用于同步用户的用户名
 master_password = '123456',##前面在主库创建的用于同步用户的密码 
 master_port = 3306,##主库端口（可以使容器的端口，也可以是容器与主机对应的主机端口，但必须与master_host对应）
 master_log_file = 'mysql-bin.000001',##必须与前面主库查询的 File 相同 
 master_log_pos = 358,##开始读取的位置，应与前面主库查询的 Position 相同 
 master_connect_retry = 30;##如果连接失败，重试的时间间隔，单位是秒，默认是60秒

START SLAVE;##在从库中开启同步 (停止 stop slave)
##------------------------若将此从库设置为其他从库复制的主库时，进行或许的设置-------------------------------
## 停止io线程,将无法同步主库日志文件，STOP SLAVE IO_THREAD;
## 停止sql线程，将无法执行同步主库的sql,STOP SLAVE SQL_THREAD;
##创建供其他的从库复制同步的用户
#创建slave用户
CREATE USER 'slave_01'@'%' IDENTIFIED BY '123456';
#授予REPLICATION SLAVE, REPLICATION CLIENT权限
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave_01'@'%';
```



+ 10.创建第二个从库

  ```shell
  docker run --name mysql_slave_02 -p 3308:3306 -e MYSQL_ROOT_PASSWORD=123456 -d -v /home/mysql/slave_02.cnf:/etc/mysql/my,cnf mysql:5.7
  ```

  slave_02.cnf
  
  ```shell
  [mysqld]
  ```
## 设置server_id,注意要唯一
  server-id=2 
```
  

  
  ```mysql
  ##进入第一个从库并查询
  show master status;
```

  

  ![image-20201221182459249](..\images\image-20201221182459249.png)

  ```mysql
  ##进入从数据库
CHANGE MASTER TO master_host = '172.17.0.3', ##主库ip(第一个从库ip)
   master_user = 'slave_01',##前面第一个从库创建的用于同步用户的用户名
   master_password = '123456',##前面第一个从库创建的用于同步用户的密码 
   master_port = 3306,##主库端口
   master_log_file = 'mysql-slave-bin.000004',##必须与File 相同 
   master_log_pos = 154,##开始读取的位置，应与前面主库查询的 Position 相同 
   master_connect_retry = 30;##如果连接失败，重试的时间间隔，单位是秒，默认是60秒
  
  START SLAVE;
  ```

  

### 双主双从

搭建两套主从后，两主互相复制

两主配置文

```xml
#M1（第一个主库）
[mysqld]
basedir =/usr/local/mysql
datadir =/usr/local/mysql/data
port = 3306
server_id = 1
log-bin= mysql-bin
binlog_format = mixed
read-only=0
#binlog-do-db=test
binlog-ignore-db=mysql
binlog-ignore-db=information_schema
binlog-ignore-db=performance_schema
auto-increment-offset=1
auto-increment-increment=2
#S1（第一个从库）
[mysqld]
basedir =/usr/local/mysql
datadir =/usr/local/mysql/data
port = 3306
server_id = 2
log-bin= mysql-bin
binlog_format = mixed
#replicate-do-db=test
replicate-ignore-db=mysql
replicate-ignore-db=information_schema
replicate-ignore-db=performance_schema
relay_log=mysql-relay-bin
log-slave-updates=on
#M2（第二个主库）
[mysqld]
basedir =/usr/local/mysql
datadir =/usr/local/mysql/data
port = 3306
server_id = 3
log-bin= mysql-bin
binlog_format = mixed
read-only=0
#binlog-do-db=test
binlog-ignore-db=mysql
binlog-ignore-db=information_schema
binlog-ignore-db=performance_schema
auto-increment-offset=2
auto-increment-increment=2
#S2（第二个从库）
[mysqld]
basedir =/usr/local/mysql
datadir =/usr/local/mysql/data
port = 3306
server_id = 4
log-bin= mysql-bin
binlog_format = mixed
#replicate-do-db=test
replicate-ignore-db=mysql
replicate-ignore-db=information_schema
replicate-ignore-db=performance_schema
relay_log=mysql-relay-bin
log-slave-updates=on
#属性说明
log-bin ：需要启用二进制日志
server_id : 用于标识不同的数据库服务器
binlog_format:binary log的模式，三种
binlog-do-db : 需要记录到二进制日志的数据库
binlog-ignore-db : 忽略记录二进制日志的数据库
auto-increment-offset :该服务器自增列的初始值。
auto-increment-increment :该服务器自增列增量。
replicate-do-db ：指定复制的数据库
replicate-ignore-db ：不复制的数据库
relay_log ：从库的中继日志，主库日志写到中继日志，中继日志再重做到从库。
log-slave-updates ：该从库是否写入二进制日志，如果需要成为多主则可启用。只读可以不需要。

如果为多主的话注意设置 auto-increment-offset 和 auto-increment-increment
如上面为双主的设置：
 自增列显示为：1,3,5,7,……（offset=1，increment=2）
 自增列显示为：2,4,6,8,……（offset=2，increment=2）
```

