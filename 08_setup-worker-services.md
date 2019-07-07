
```
./scripts/setup-worker-services
```

## step 01:
```

for i in {0..2}; do
  cat <<'EOF' | vagrant ssh "worker-${i}" -- sudo bash
set -euo pipefail

apt-get update
apt-get install -y socat libgpgme11

mkdir -p \
  /etc/containers \
  /etc/cni/net.d \
  /etc/crio \
  /opt/cni/bin \
  /usr/local/libexec/crio \
  /var/lib/kubelet \
  /var/lib/kube-proxy \
  /var/lib/kubernetes \
  /var/run/kubernetes

tar -xvf /vagrant/tools/cni-plugins-amd64-v0.6.0.tgz -C /opt/cni/bin/

cp /vagrant/tools/runc /usr/local/bin/
cp /vagrant/tools/{crio,kube-proxy,kubelet,kubectl} /usr/local/bin/
cp /vagrant/tools/{conmon,pause} /usr/local/libexec/crio/
cp /vagrant/tools/{crio.conf,seccomp.json} /etc/crio/
cp /vagrant/tools/policy.json /etc/containers/

cp "/vagrant/config/$(hostname)-10-bridge.conf" /etc/cni/net.d/10-bridge.conf
cp /vagrant/config/99-loopback.conf /etc/cni/net.d
cp "/vagrant/config/$(hostname)-crio.service" /etc/systemd/system/crio.service

cp /vagrant/config/kube-proxy.kubeconfig /var/lib/kube-proxy/kubeconfig

cp /vagrant/certificates/ca.pem /var/lib/kubernetes/
cp "/vagrant/certificates/$(hostname).pem" "/vagrant/certificates/$(hostname)-key.pem" /var/lib/kubelet
cp "/vagrant/config/$(hostname).kubeconfig" /var/lib/kubelet/kubeconfig
cp /vagrant/config/$(hostname)-kubelet-config  /var/lib/kubelet/kubelet-config.yaml

cp "/vagrant/config/$(hostname)-kubelet.service" /etc/systemd/system/kubelet.service

cp /vagrant/config/kube-proxy.service /etc/systemd/system/

systemctl daemon-reload
systemctl enable crio kubelet kube-proxy
systemctl start crio kubelet kube-proxy
EOF
done

```


## step 02:
```

vagrant ssh controller-0
kubectl get nodes


```

```
NAME       STATUS    AGE       VERSION
worker-0   Ready     1m        v1.3.0
worker-1   Ready     55s       v1.3.0
worker-2   Ready     12s       v1.3.0
```




