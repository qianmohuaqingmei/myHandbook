## 容器监控系统

##### 1 启动influxdb

```bash
# docker run -d -p 8083:8083 \
  -p 8086:8086 \
  --expose 8090 \
  --expose 8099 \
  --name influxsrv \
  tutum/influxdb:0.10
```

##### 2 初始化influxdb数据库

2.1 进入influxsrv容器

```bash
# docker exec -it influxsrc /bin/bash

```

2.2 进入influx数据库

```bash
# influx
```

2.3 创建cadvisor表

```bash
# CREATE DATABASE cadvisor
```

2.4 查看表

```bash
# SHOW DATABASES
```

2.5 使用表

```bash
# use cadvisor
```

2.6 创建root用户和密码root

```bash
# CREATE USER "root" WITH PASSWORD 'root' WITH ALL PRIVILEGES
```

2.7 查看用户

```bash
# show users
```

2.8 退出数据库

```bash
# exit
```

2.9 退出容器

```bash
# exit
```

##### 3 创建cadvisor容器

```bash
# docker run -d --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:rw \
  --volue=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --publish=8080:8080 \
  --detach=true \
  --link influxsrv:influxsrv \
  --name=cadvisor \
  google/cadvisor:latest \
  --storage_driver=influxdb \
  --storage_driver_db=cadvisor \
  --storage_driver_host=influxsrv:8086
```

##### 4 创建grafana容器

```bash
# docker run -d -p 3000:3000 -e INFLUXDB_HOST=127.0.0.1 \
  -e INFLUXDB_PORT=8086 \
  -e INFLUXDB_NAME=cadvisor \
  -e INFLUXDB_USER=root \
  -e INFLUXDB_PASS=root \
  --link influxsrv:influxsrv \
  --name grafana \
  grafana/grafana:latest 
```

##### 5 测试验证

进入influxdb容器

```bash
# docker exec -it influxdb /bin/bash
```

构造influxdb数据

```bash
# influx
# user cadvisor
# INSERT cpu,host=test,region=us_west value=0.4
# SELECT * FROM /.*/ LIMIT 1
```


---

### 另外一种启动方式：分布式启动

##### 1 启动influxdb容器

```bash
# docker run -d -p 8083:8083 \
  -p 8086:8086 \
  --expose 8090 \
  --expose 8099 \
  --name influxsrv \
  tutum/influxdb:0.10
```
容器启动后，进入容器将配置文件中的hostname修改为实际的IP


##### 2 初始化influxdb数据库

2.1 进入influxsrv容器

```bash
# docker exec -it influxsrc /bin/bash

```

2.2 进入influx数据库

```bash
# influx
```

2.3 创建testdb表

```bash
# CREATE DATABASE testdb
```

2.4 查看表

```bash
# SHOW DATABASES
```

2.5 使用表

```bash
# use testdb
```

2.6 创建root用户和密码root

```bash
# CREATE USER "root" WITH PASSWORD 'root' WITH ALL PRIVILEGES
```

2.7 查看用户

```bash
# show users
```
2.8 自定义一条数据

```bash
# INSERT cpu,host=test,region=us_west value=0.64
# SELECT * FROM /.*/ LIMIT 1
```

2.9 退出数据库

```bash
# exit
```

2.10 退出容器

```bash
# exit
```

##### 3 创建grafana容器

```bash
# docker run -d --restart=always \
  -p 3000:3000 \
  -v /tmp/grafan_db:/var/lib/grafana \
  -e "GF_SECURITY_ADMIN_PASSWORD=admin"\
  --name grafana \
  grafana/grafana:latest 
```

##### 4 创建cadvisor容器

```bash
# docker run -d --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:rw \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  google/cadvisor:latest \
  --storage_driver=influxdb \
  --storage_driver_db=testdb \
  --storage_driver_host=10.0.0.8:8086
```
