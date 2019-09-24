## Consul容器启动

##### 1 单节点

启动容器

```bash
# docker run --restart=always -d -h node \
  -v /mnt:/data \
  -p 8300:8300 \
  -p 8301:8301 \
  -p 8301:8301/udp \
  -p 8302:8302 \
  -p 8302:8302/udp \
  -p 8400:8400 \
  -p 8500:8500 \
  -p 8600:8600/upd \
  consul:latest -server -advertise 10.0.0.8 -bootstrap-expect 1 -dc=Duzi
```

##### 2 consul server带UI

启动容器

```bash
# docker run --restart=always -d \
  --name consul-app \
  -p 10.0.0.8:8400:8400 \
  -p 10.0.0.8:8500:8500 \
  -p 10.0.0.8:8600:53/udp \
  -h consul consul:latest -server bootstrap -ui-dir /ui
```

##### 3 consul同一个数据中心的集群

```bash
# docker run --restart=always -d \
  --name consul-app1 \
  -h node1 \
  -v /mnt:/data \
  -p 8300:8300 \
  -p 8301:8301 \
  -p 8301:8301/udp \
  -p 8302:8302 \
  -p 8302:8302/udp \
  -p 8400:8400 \
  -p 8500:8500 \
  -p 8600:8600/upd \
  consul:latest -server -advertise 10.0.0.8 -bootstrap-expect 3 -dc=Duzi
```

```bash
# docker run --restart=always -d \
  --name consul-app2 \
  -h node2 \
  -v /mnt:/data \
  -p 8300:8300 \
  -p 8301:8301 \
  -p 8301:8301/udp \
  -p 8302:8302 \
  -p 8302:8302/udp \
  -p 8400:8400 \
  -p 8500:8500 \
  -p 8600:8600/upd \
  consul:latest -server -advertise 10.0.0.9 -bootstrap-expect 3 -dc=Duzi
```

```bash
# docker run --restart=always -d \
  --name consul-app3 \
  -h node3 \
  -v /mnt:/data \
  -p 8300:8300 \
  -p 8301:8301 \
  -p 8301:8301/udp \
  -p 8302:8302 \
  -p 8302:8302/udp \
  -p 8400:8400 \
  -p 8500:8500 \
  -p 8600:8600/upd \
  consul:latest -server -advertise 10.0.0.10 -bootstrap-expect 3 -dc=Duzi
```

##### 4 consul两个数据中心的集群

启动第一个数据中心的集群容器

```bash
# docker run --restart=always -d \
  --name consul-app \
  -h node \
  -v /mnt:/data \
  -p 8300:8300 \
  -p 8301:8301 \
  -p 8301:8301/udp \
  -p 8302:8302 \
  -p 8302:8302/udp \
  -p 8400:8400 \
  -p 8500:8500 \
  -p 8600:8600/upd \
  consul:latest -server -advertise 10.0.0.11 -bootstrap-expect 1 -dc=Duzi01
```


启动第二个数据中心的集群容器

```bash
# docker run --restart=always -d \
  --name consul-app \
  -h node \
  -v /mnt:/data \
  -p 8300:8300 \
  -p 8301:8301 \
  -p 8301:8301/udp \
  -p 8302:8302 \
  -p 8302:8302/udp \
  -p 8400:8400 \
  -p 8500:8500 \
  -p 8600:8600/upd \
  consul:latest -server -advertise 10.0.0.12 -bootstrap-expect 1 -dc=Duzi02
```

将consul工具复制到主机上

```bash
# docker cp consul-app:/bin/consul /usr/local/bin/
```

先查看wan的节点数
```bash
# consul members -wan
```

将第二个server的节点加入到wan里面
```bash
# consul join -wan 10.0.0.12
```

这样就可以将两个数据中心连接起来，可以查看wan上有哪些数据中心
```bash
# curl http://localhost:8500/v1/catalog/datacenters
```

查看每个数据中心的信息
```bash
# curl http://localhost:8500/v1/catalog/nodes?dc=Duzi01

# curl http://localhost:8500/v1/catalog/nodes?dc=Duzi02
```

参考

- [consul join](https://www.consul.io/docs/commands/join.html)
