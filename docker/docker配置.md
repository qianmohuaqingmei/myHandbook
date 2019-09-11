# Docker 相关配置

##### 1 配置代理

```bash
# vi /etc/sysconfig/docker

HTTP_PROXY="http://127.0.0.1:8080"
HTTPS_PROXY="https://127.0.0.1:8080"
http_proxy="${HTTP_PROXY}"
https_proxy="${HTTPS_PROXY}"
```

##### 2 配置私有仓库

```bash
# vi /etc/sysconfig/docker
OPTIONS='--host=unix:///var/run/docker.sock --insecure-registry=127.0.0.1:500 --selinux-enabled' 
```

##### 3 登录私有仓库

```bash
# docker login -u <username> -p <password> -e <email> 127.0.0.1:5000
```

##### 4 启动docker

```bash
# HTTP_PROXY=http://127.0.0.1:8080 HTTPS_PROXY=https://172.0.0.1:8080 \
  /opt/duzi/docker/docker --daemon -s devicemapper \
  --storage-opt dm.override_udev_sync_check=true \
  --storage-opt dm.fs=xfs \
  --storage-opt dm.loopdatasize=45G \
  --storage-opt dm.basesize=45G \
  --storage-opt dm.blkdiscard=false \
  --storage-opt dm.mountopt=nodiscard \
  --exec-opt native_cgroupdriver=cgroupfs \
  --host unix:///var/run/docker.sock \
  --insecure-registry 127.0.0.1:5000 \
  --insecure-registry quay.io \
  --pidfile /opt/duzi/docker/docker.pid \
  1 >> /var/log/duzi/docker/run/docker.stderr.log 2>&1 & 
```