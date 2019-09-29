## Consul 模板

KEY刷新的三种方式

##### 1 KEY

```metadata json
{[key "DCM/${GREION_ID}/CONFIG/g_current_region/${SERVICE_NAME}/ips/${LOCAL_IP}" ]}
```

##### 2 KEYORDEFAULT

```metadata json
{[keyOrDefault "DCM/${GREION_ID}/CONFIG/g_current_region/${SERVICE_NAME}/ips/${LOCAL_IP}" "10.0.0.8" ]}
```

##### 3 PLUGIN插件

```metadata json
{[ if ls "DCM/${GREION_ID}/CONFIG/g_current_region/${SERVICE_NAME}/ips" ]}
{[ $etcd_ips := ls "DCM/${GREION_ID}/CONFIG/g_current_region/${SERVICE_NAME}/ips" |explode |toJSON |plugin "/etc/dcm/plugin.py" "https://" "2379" ]}
etcd.url = "{[ $etcd_ips ]}"
else
etcd.url = ""
{[ end ]}
```

plugin 内容如下

```python
#!/usr/bin/python
# -*- coding:utf-8 -*-

import sys
import json
import string

if len(sys.argv) == 2:
    result = []
    try:
        content = json.loads(sys.argv[1])
    except:
        pass
    for key in content:
        result.append(key)
    print(",".join(result))
```

