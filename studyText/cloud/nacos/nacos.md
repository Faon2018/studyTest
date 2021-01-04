# nacos

docker部署nacos服务

启动:

```shell
docker run --name nacos -e MODE=standalone -p 8848:8848 -d nacos
```

修改数据库配置：

```shell
#进入nacos容器
docker exec -it nacos /bin/bahs
vim /conf/application.properties

#增加
# db mysql
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://IP:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=123456
#重启容器生效
```

