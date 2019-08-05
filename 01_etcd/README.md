# etcd集群的搭建和验证



| 名称 | 位置 |
| --- | --- |
| etcd | /usr/local/bin/etcd |
| etcdctl | /usr/local/bin/etcdctl |
| etcd.service | /lib/systemd/system/etcd.service |
| etcd.conf | /etc/etcd/etcd.conf |






## 下载二进制镜像

```

wget 
https://github.com/etcd-io/etcd/releases/download/v3.3.9/etcd-v3.3.9-linux-amd64.tar.gz

tar -xf  etcd-v3.3.9-linux-amd64.tar.gz


```

## 开启主机


```
vagrant init xxxxx

vagrant  up

```


