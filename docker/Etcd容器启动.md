## Etcd容器启动

##### 1 单节点

启动容器

```bash
# docker run --restart=always -d \
  --name etcd-app \
  -p 4001:4001 \
  -p 2380:2380 \
  -p 2379:2379 \
  etcd:v2.2.1 \
  -name etcd-node \
  -advertise-client-urls http://10.0.0.8:2379,http://10.0.0.8:4001 \
  -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
  -initial-advertise-peer-urls http://10.0.0.8:2380 \
  -listen-peer-urls http://0.0.0.8:2380 \
  -initial-cluster-token etcd-cluster-1 \
  -initial-cluster etcd-node=http://10.0.0.8:2380 \
  --initial-cluster-state new
```

##### 2 集群

启动容器

```bash
# docker run --restart=always -d \
  --name etcd-app \
  -p 4001:4001 \
  -p 2380:2380 \
  -p 2379:2379 \
  etcd:v2.2.1 \
  -name etcd-node1 \
  -advertise-client-urls http://10.0.0.8:2379,http://10.0.0.8:4001 \
  -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
  -initial-advertise-peer-urls http://10.0.0.8:2380 \
  -listen-peer-urls http://0.0.0.0:2380 \
  -initial-cluster-token etcd-cluster-1 \
  -initial-cluster etcd-node1=http://10.0.0.8:2380,etcd-node2=http://10.0.0.9:2380,etcd-node3=http://10.0.0.10:2380 \
  --initial-cluster-state new
```

```bash
# docker run --restart=always -d \
  --name etcd-app \
  -p 4001:4001 \
  -p 2380:2380 \
  -p 2379:2379 \
  etcd:v2.2.1 \
  -name etcd-node2 \
  -advertise-client-urls http://10.0.0.9:2379,http://10.0.0.9:4001 \
  -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
  -initial-advertise-peer-urls http://10.0.0.9:2380 \
  -listen-peer-urls http://0.0.0.0:2380 \
  -initial-cluster-token etcd-cluster-1 \
  -initial-cluster etcd-node1=http://10.0.0.8:2380,etcd-node2=http://10.0.0.9:2380,etcd-node3=http://10.0.0.10:2380 \
  --initial-cluster-state new
```

```bash
# docker run --restart=always -d \
  --name etcd-app \
  -p 4001:4001 \
  -p 2380:2380 \
  -p 2379:2379 \
  etcd:v2.2.1 \
  -name etcd-node3 \
  -advertise-client-urls http://10.0.0.10:2379,http://10.0.0.10:4001 \
  -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
  -initial-advertise-peer-urls http://10.0.0.10:2380 \
  -listen-peer-urls http://0.0.0.0:2380 \
  -initial-cluster-token etcd-cluster-1 \
  -initial-cluster etcd-node1=http://10.0.0.8:2380,etcd-node2=http://10.0.0.9:2380,etcd-node3=http://10.0.0.10:2380 \
  --initial-cluster-state new
```

查看leader
```bash
# curl http://10.0.0.8:2379/v2/stats/leader
```