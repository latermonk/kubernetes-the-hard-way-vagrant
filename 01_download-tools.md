
##  k8s版本

```
https://storage.googleapis.com/kubernetes-release/release/stable.txt

readonly k8s_version="v1.13.0"

readonly etcd_version="v3.3.9"

readonly cfssl_version="R1.2"

readonly traefik_version="v1.3.8"

readonly crio_version="v1.11.2"
```


## 下载二进制文件


```
curl -sSL \
  -O "https://storage.googleapis.com/kubernetes-release/release/${k8s_version}/bin/linux/amd64/kube-apiserver" \
  -O "https://storage.googleapis.com/kubernetes-release/release/${k8s_version}/bin/linux/amd64/kube-controller-manager" \
  -O "https://storage.googleapis.com/kubernetes-release/release/${k8s_version}/bin/linux/amd64/kube-scheduler" \
  -O "https://storage.googleapis.com/kubernetes-release/release/${k8s_version}/bin/linux/amd64/kube-proxy" \
  -O "https://storage.googleapis.com/kubernetes-release/release/${k8s_version}/bin/linux/amd64/kubelet" \
  -O "https://storage.googleapis.com/kubernetes-release/release/${k8s_version}/bin/linux/amd64/kubectl"


```

## 下载 etcd
```

curl -sSL \
  -O "https://github.com/coreos/etcd/releases/download/${etcd_version}/etcd-${etcd_version}-linux-amd64.tar.gz"


tar -xf "etcd-${etcd_version}-linux-amd64.tar.gz"

```

##  下载 cni-plugins &&  runc && crio
```

curl -sSL \
  -O "https://github.com/containernetworking/plugins/releases/download/v0.6.0/cni-plugins-amd64-v0.6.0.tgz" \
  -O "https://github.com/opencontainers/runc/releases/download/v1.0.0-rc4/runc.amd64" \
  -O "https://files.schu.io/pub/cri-o/crio-amd64-${crio_version}.tar.gz"

tar -xf "crio-amd64-${crio_version}.tar.gz"

mv runc.amd64 runc

```

##  下载 traefik


```

curl -sSL \
  -O "https://github.com/containous/traefik/releases/download/${traefik_version}/traefik_linux-amd64"

mv traefik_linux-amd64 traefik

chmod +x \
  kube-apiserver \
  kube-controller-manager \
  kube-scheduler \
  kube-proxy \
  kubelet \
  kubectl \
  runc \
  traefik

```

