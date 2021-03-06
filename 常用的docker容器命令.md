## 常用的docker容器命令

```
# postgre启动命令，并且设置连接数
docker run -itd --name postgresql --restart always -v /opt/data/postgresql:/var/lib/postgresql/data -p 5432:5432 -e POSTGRES_USER=sde -e POSTGRES_PASSWORD=postgres postgres:9.6.1 postgres -c max_connections=500
docker run -itd --restart always -p 5432:5432 -v /cityworks/postgresql:/var/lib/postgresql/data --name postgres -e POSTGRES_USER=sde -e POSTGRES_PASSWORD=postgres postgres:9.6.1

# mongo启动命令
docker run --name mongo --restart always -v /opt/data/mongo/data:/data/db -p 27017:27017 -d mongo:3.4.5

# minio启动命令
docker run -it -p 9000:9000 --restart always --name minio -e MINIO_ACCESS_KEY=minio -e MINIO_SECRET_KEY=minio.minio minio/minio server /export

# redis启动命令
docker run -itd --restart always --name redis -p 6379:6379 redis:4.0.9-alpine

# rebbitMQ启动命令
docker run -itd --restart always --name rabbitmq -p 4369:4369 -p 5671:5671 -p 5672:5672 -p 25672:25672 -p 15671:15671 -p 15672:15672 rabbitmq:3.6.10-management-alpine

# gitbook启动命令
docker run -itd --name gitbook --restart always -v /opt/soft/gitbook/soa:/gitbook -p 4000:4000 fellah/gitbook:3.2 sh

# MySQL启动命令
docker run -itd --name mysql -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql:5.7.3

### MySQL5.7设置时区
docker run -itd --restart always -e MYSQL_ROOT_PASSWORD=root -v /etc/localtime:/etc/localtime:ro -p 3306:3306 --name mysql mysql:5.7.16 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --init-connect='SET NAMES utf8mb4;' --default-time-zone='+8:00' --innodb-flush-log-at-trx-commit=0 --log-timestamps=SYSTEM --sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'

docker run -itd --restart always -e MYSQL_ROOT_PASSWORD=root -v /etc/localtime:/etc/localtime:ro -p 3306:3306 --name mysql mysql:5.7.16 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --init-connect='SET NAMES utf8mb4;' --default-time-zone='+8:00' --log-timestamps=SYSTEM --sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'

### MySQL8设置时区
docker run -itd --restart always -e MYSQL_ROOT_PASSWORD=root -v /etc/localtime:/etc/localtime:ro -p 3306:3306 --name mysql mysql:8.0.11 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --init-connect='SET NAMES utf8mb4;' --default-time-zone='+8:00' --log-timestamps=SYSTEM --sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION' --default_authentication_plugin=mysql_native_password

# Swagger-editor启动命令
docker run -itd --restart always --name swagger-editor -p 18080:8080 swaggerapi/swagger-editor:v3.5.3
# Swagger-ui启动命令
docker run -itd --restart always --name swagger-ui -p 18081:8080 swaggerapi/swagger-ui:3.17.6
docker run -itd --restart always --name swagger-ui -p 80:8080 -e API_URL=http://generator.swagger.io/api/swagger.json swaggerapi/swagger-ui:3.17.6

# neo4j容器命令
docker run -itd --restart always \
--name neo4j \
-e NEO4j_AUTH=neo4j/admin123 \
-e NEO4J_dbms_tx__log_rotation_retention__policy=true \
-e NEO4J_dbms_memory_pagecache_size=128M \
-e NEO4J_dbms_memory_heap_initial__size=128M \
-e NEO4J_dbms_memory_heap_max__size=256M \
-p 7474:7474 -p 7473:7473 -p 7687:7687 \
neo4j:3.4.5
```

#### 通过docker inspect常用名命令
```
# 查看容器IP(下面三条命令查看不了自定义网卡的容器IP)
docker inspect --format '{{ .NetworkSettings.IPAddress }}' 容器名
docker inspect 容器名
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' 容器名
docker inspect 容器名 | grep "IPAddress" -n

# 要获取所有容器名称及其IP地址只需一个命令
docker inspect -f '{{.Name}} - {{.NetworkSettings.IPAddress }}' $(docker ps -aq)
```

#### 通过docker创建网段，分配容器IP
```
docker network rm docker-swarm-network
docker network create --subnet=10.10.10.0/24 docker-swarm-network
docker run -itd --name manager1 --net docker-swarm-network --ip 10.10.10.101 --restart always docker:18.03.1-ce
```

> docker跑的服务都不应该以后台的形式运行,docker检测不了后台运行服务的状态,是否是成功或者失败,导致docker启动的时候不断地restart, 例如redis以后台形式运行, docker run -itd --restart always redis redis-server --daemonize yes , 该命令启动的redis服务会失败

#### 查找容器名的部分名词字段
```
docker ps -a --filter name=redis -q
# 7c16765f5ef3
# ecba9578534c
# c1353a0c7231
# 8a3339228398
docker stop `docker ps -a -q --filter name=redis`
docker rm `docker ps -a -q --filter name=redis`
```

#### 容器环境的必备参数
```
-e TZ=Asia/Shanghai -v /etc/localtime:/etc/localtime:ro
```

#### 导出和导入docker镜像
```
docker save mysql:5.7.21 > mysql-5.7.21.tar.gz
docker load < mysql-5.7.21.tar.gz
```

### maven仓库的网页查看地址[http://maven.aliyun.com/mvn/view]()
仓库|仓库类型|仓库存储策略|阿里云的映射地址|
:---:|:---:|:---:|:---:
Repository | Type | Policy | Path
apache snapshots | proxy | SNAPSHOT | https://maven.aliyun.com/repository/apache-snapshots
central | proxy | RELEASE | https://maven.aliyun.com/repository/central
google  |proxy | RELEASE | https://maven.aliyun.com/repository/google
gradle-plugin | proxy | RELEASE	| https://maven.aliyun.com/repository/gradle-plugin
jcenter	| proxy	| RELEASE | https://maven.aliyun.com/repository/jcenter
spring | proxy | RELEASE | https://maven.aliyun.com/repository/spring
spring-plugin | proxy | RELEASE	| https://maven.aliyun.com/repository/spring-plugin
public | group | RELEASE | https://maven.aliyun.com/repository/public
releases | hosted | RELEASE | https://maven.aliyun.com/repository/releases
snapshots | hosted | SNAPSHOT | https://maven.aliyun.com/repository/snapshots
grails-core | proxy | RELEASE |https://maven.aliyun.com/repository/grails-core

