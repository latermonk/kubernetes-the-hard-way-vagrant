# etcd集群的搭建和验证



| 名称 | 位置 |
| --- | --- |
| etcd | /usr/local/bin/etcd |
| etcdctl | /usr/local/bin/etcdctl |
| etcd.service | /lib/systemd/system/etcd.service |
| etcd.conf | /etc/etcd/etcd.conf |
| *.pem | /etc/etcd/*.pem |






## 下载二进制镜像

```

wget 
https://github.com/etcd-io/etcd/releases/download/v3.3.9/etcd-v3.3.9-linux-amd64.tar.gz

tar -xf  etcd-v3.3.9-linux-amd64.tar.gz


```

## etcd etcdctl


```
chmod +x  etc*

mv  etc*   /usr/local/bin



```

##  *.pems


##   etcd.service



```
[Unit]
Description=etcd
Documentation=https://github.com/coreos

[Service]
ExecStart=/usr/local/bin/etcd \
  --name controller-0 \
  --cert-file=/etc/etcd/kubernetes.pem \
  --key-file=/etc/etcd/kubernetes-key.pem \
  --peer-cert-file=/etc/etcd/kubernetes.pem \
  --peer-key-file=/etc/etcd/kubernetes-key.pem \
  --trusted-ca-file=/etc/etcd/ca.pem \
  --peer-trusted-ca-file=/etc/etcd/ca.pem \
  --peer-client-cert-auth \
  --client-cert-auth \
  --initial-advertise-peer-urls https://192.168.199.10:2380 \
  --listen-peer-urls https://192.168.199.10:2380 \
  --listen-client-urls https://192.168.199.10:2379,http://127.0.0.1:2379 \
  --advertise-client-urls https://192.168.199.10:2379 \
  --initial-cluster-token etcd-cluster-0 \
  --initial-cluster controller-0=https://192.168.199.10:2380,controller-1=https://192.168.199.11:2380,controller-2=https://192.168.199.12:2380 \
  --initial-cluster-state new \
  --data-dir=/var/lib/etcd
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target

```


