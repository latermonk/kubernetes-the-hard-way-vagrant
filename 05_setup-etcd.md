## 


```
for i in {0..2}; do
  cat <<EOF | vagrant ssh "controller-${i}" -- sudo bash

cp "/vagrant/tools/etcd-${etcd_version}-linux-amd64/etcd"* /usr/local/bin

mkdir -p /etc/etcd /var/lib/etcd

cp \
  /vagrant/certificates/ca.pem \
  /vagrant/certificates/kubernetes-key.pem \
  /vagrant/certificates/kubernetes.pem \
  /etc/etcd/
cp \
  "/vagrant/config/\$(hostname)-etcd.service" \
  /etc/systemd/system/etcd.service

systemctl daemon-reload
systemctl enable etcd
systemctl start etcd
EOF
done
```


##  check the etcd cluster


```
vagrant ssh controller-0
ETCDCTL_API=3 etcdctl member list

6c500a9f4f9113de, started, controller-0, https://192.168.199.10:2380, https://192.168.199.10:2379
e206d150eae73959, started, controller-2, https://192.168.199.12:2380, https://192.168.199.12:2379
e7e775a3da74a469, started, controller-1, https://192.168.199.11:2380, https://192.168.199.11:2379
```

