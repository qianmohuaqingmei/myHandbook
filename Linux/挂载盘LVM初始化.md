### 挂载盘LVM初始化

#### 1 查看挂载盘名称

```bash
# fdisk -l
``` 

#### 2 将挂载盘分区

```bash
# fdisk /dev/vdb
```

#### 3 格式化分区

```bash
# mkfs.ext3 /dev/vdb1
or
# mkfs.ext4 /dev/vdb1
or
# mkfs.xfs /dev/vdb1
```

#### 4 创建挂载目录

```bash
# mkdir -p /opt/duzi
```

#### 5 挂载目录

```bash
# mount /dev/vdb1 /opt/duzi
``` 

#### 6 将挂载盘信息写入/etc/fstab文件

```bash
# echo "/dev/vdb1  /opt/duzi ext3 defaults 0 0" >>/etc/fstab
or
# echo "/dev/vdb1  /opt/duzi xfs defaults 0 0" >>/etc/fstab
or
# echo "/dev/vdb1  /opt/duzi ext4 defaults 0 0" >>/etc/fstab
```

附录

##### 查看磁盘分区的UUID

```bash
# ls -l /dev/disk/by-uuid/
```


