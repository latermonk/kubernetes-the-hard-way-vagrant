##  step 01:

```
./scripts/setup-controller-services

```

```

for i in {0..2}; do
  cat <<'EOF' | vagrant ssh "controller-${i}" -- sudo bash

mkdir -p /var/lib/kubernetes

cp \
  /vagrant/certificates/ca.pem \
  /vagrant/certificates/ca-key.pem \
  /vagrant/certificates/kubernetes-key.pem \
  /vagrant/certificates/kubernetes.pem \
  /vagrant/config/encryption-config.yaml \
  /var/lib/kubernetes/

cp \
  /vagrant/tools/kube-apiserver \
  /vagrant/tools/kube-controller-manager \
  /vagrant/tools/kube-scheduler \
  /vagrant/tools/kubectl \
  /usr/local/bin/

cp \
  "/vagrant/config/$(hostname)-kube-apiserver.service" \
  /etc/systemd/system/kube-apiserver.service
cp \
  "/vagrant/config/$(hostname)-kube-scheduler.service" \
  /etc/systemd/system/kube-scheduler.service
cp \
  "/vagrant/config/$(hostname)-kube-controller-manager.service" \
  /etc/systemd/system/kube-controller-manager.service

systemctl daemon-reload
systemctl enable kube-apiserver kube-controller-manager kube-scheduler
systemctl start kube-apiserver kube-controller-manager kube-scheduler
EOF

  until vagrant ssh "controller-${i}" -- curl --ipv4 --fail --silent --max-time 3 "http://127.0.0.1:8080/api" &>/dev/null; do
    echo "Waiting for kube-apiserver to become ready in controller-${i}..."
    sleep 3
  done

done

```



## step 02:

```

for c in controller-0 controller-1 controller-2; do vagrant ssh $c -- kubectl get componentstatuses; done


```

result:


```
NAME                 STATUS    MESSAGE              ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-1               Healthy   {"health": "true"}
etcd-2               Healthy   {"health": "true"}
etcd-0               Healthy   {"health": "true"}
[...]
```


