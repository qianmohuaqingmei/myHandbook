### 删除状态异常的挂盘

有时候虚拟机删掉了，但是其挂盘还没删掉，且这时候的状态不是显示available,使用force-delete强制删除都没生效。

##### 1 导入环境变量

```bash
# source keystone_admin
```

##### 2 找到该挂盘

```bash
# cinder list --all
```

##### 3 将状态重置为available

```bash
# cinder reset-state --state available <cinder-id>
```

##### 4 删除volume

```bash
# cinder delete <cinder-id>
```

参考:

[How to delete volume](https://ask.openstack.org/en/question/66918/how-to-delete-volume-with-available-status-and-attached-to/)
