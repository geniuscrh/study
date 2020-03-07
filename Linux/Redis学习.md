# 客户端软件

Redis Desktop Manager



搭建单体Redis

```yaml
version: '3.1'
services:
 redis:
  container_name: wangluo-redis
  restart: always
  ports:
   - 6379:6379  //端口号
  image: redis //docker 镜像
  command: redis-server --requirepass ${pwd} --notify-keyspace-events Eglx

```





# 搭建 Redis 集群

搭建一主两从环境，docker-compose.yml 配置如下：

```yml
version: '3.1'
services:
  master:
    image: redis
    container_name: redis-master
    ports:
      - 6379:6379

  slave1:
    image: redis
    container_name: redis-slave-1
    ports:
      - 6380:6379
    command: redis-server --slaveof redis-master 6379

  slave2:
    image: redis
    container_name: redis-slave-2
    ports:
      - 6381:6379
    command: redis-server --slaveof redis-master 6379
```

# 搭建 Sentinel 集群

我们至少需要创建三个 Sentinel 服务，docker-compose.yml 配置如下：

```yaml
version: '3.1'
services:
  sentinel1:
    image: redis
    container_name: redis-sentinel-1
    ports:
      - 26379:26379
    command: redis-sentinel /usr/local/etc/redis/sentinel.conf
    volumes:
      - ./sentinel1.conf:/usr/local/etc/redis/sentinel.conf

  sentinel2:
    image: redis
    container_name: redis-sentinel-2
    ports:
      - 26380:26379
    command: redis-sentinel /usr/local/etc/redis/sentinel.conf
    volumes:
      - ./sentinel2.conf:/usr/local/etc/redis/sentinel.conf

  sentinel3:
    image: redis
    container_name: redis-sentinel-3
    ports:
      - 26381:26379
    command: redis-sentinel /usr/local/etc/redis/sentinel.conf
    volumes:
      - ./sentinel3.conf:/usr/local/etc/redis/sentinel.conf
```

## 修改 Sentinel 配置文件

需要三份 sentinel.conf 配置文件，分别为 `sentinel1.conf`，`sentinel2.conf`，`sentinel3.conf`，配置文件内容相同

```yaml
port 26379
dir /tmp
# 自定义集群名，其中 127.0.0.1 为 redis-master 的 ip，6379 为 redis-master 的端口，2 为最小投票数（因为有 3 台 Sentinel 所以可以设置成 2）
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 30000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
sentinel deny-scripts-reconfig yes
```

## 查看集群是否生效

进入 Sentinel 容器，使用 Sentinel API 查看监控情况：

```yaml
docker exec -it redis-sentinel-1 /bin/bash
redis-cli -p 26379
sentinel master mymaster
sentinel slaves mymaster
```









# SpringCloud应用

## pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.1.0.RELEASE</version>
</dependency>

```















