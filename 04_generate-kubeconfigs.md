# 00 generate



```
./scripts/generate-kubeconfig-kube-proxy
./scripts/generate-kubeconfig-worker
```

#  generate-kubeconfig-kube-proxy
```
cat ./scripts/generate-kubeconfig-kube-proxy
```

```
#!/bin/bash

set -euo pipefail

readonly dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

kubectl config set-cluster kubernetes-the-hard-way \
  --certificate-authority="${dir}/../certificates/ca.pem" \
  --embed-certs=true \
  --server=https://192.168.199.10:6443 \
  --kubeconfig="${dir}/../config/kube-proxy.kubeconfig"

kubectl config set-credentials kube-proxy \
  --client-certificate="${dir}/../certificates/kube-proxy.pem" \
  --client-key="${dir}/../certificates/kube-proxy-key.pem" \
  --embed-certs=true \
  --kubeconfig="${dir}/../config/kube-proxy.kubeconfig"

kubectl config set-context default \
  --cluster=kubernetes-the-hard-way \
  --user=kube-proxy \
  --kubeconfig="${dir}/../config/kube-proxy.kubeconfig"

kubectl config use-context default --kubeconfig="${dir}/../config/kube-proxy.kubeconfig"
```



## generate-kubeconfig-kube-proxy


```
kubectl config set-cluster kubernetes-the-hard-way \
  --certificate-authority="../certificates/ca.pem" \
  --embed-certs=true \
  --server=https://192.168.199.10:6443 \
  --kubeconfig="../config/kube-proxy.kubeconfig"


```

```
kubectl config set-credentials kube-proxy \
  --client-certificate="../certificates/kube-proxy.pem" \
  --client-key="../certificates/kube-proxy-key.pem" \
  --embed-certs=true \
  --kubeconfig="../config/kube-proxy.kubeconfig"
```


```
kubectl config set-context default \
  --cluster=kubernetes-the-hard-way \
  --user=kube-proxy \
  --kubeconfig="../config/kube-proxy.kubeconfig"

```


```

kubectl config use-context default --kubeconfig="../config/kube-proxy.kubeconfig"
```


**换个文件夹：**


```
kubectl config set-cluster kubernetes-the-hard-way \
  --certificate-authority="./certificates_inuse/ca.pem" \
  --embed-certs=true \
  --server=https://192.168.199.10:6443 \
  --kubeconfig="./config/kube-proxy.kubeconfig"


kubectl config set-credentials kube-proxy \
  --client-certificate="./certificates_inuse/kube-proxy.pem" \
  --client-key="./certificates_inuse/kube-proxy-key.pem" \
  --embed-certs=true \
  --kubeconfig="./config/kube-proxy.kubeconfig"



kubectl config set-context default \
  --cluster=kubernetes-the-hard-way \
  --user=kube-proxy \
  --kubeconfig="./config/kube-proxy.kubeconfig"
```


--------


#  generate-kubeconfig-worker

```
cat ./scripts/generate-kubeconfig-worker
```

```
#!/bin/bash

set -euo pipefail

readonly dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

for instance in worker-0 worker-1 worker-2; do
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority="${dir}/../certificates/ca.pem" \
    --embed-certs=true \
    --server=https://192.168.199.40:6443 \
    --kubeconfig="${dir}/../config/${instance}.kubeconfig"

  kubectl config set-credentials system:node:${instance} \
    --client-certificate="${dir}/../certificates/${instance}.pem" \
    --client-key="${dir}/../certificates/${instance}-key.pem" \
    --embed-certs=true \
    --kubeconfig="${dir}/../config/${instance}.kubeconfig"

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:${instance} \
    --kubeconfig="${dir}/../config/${instance}.kubeconfig"

  kubectl config use-context default --kubeconfig="${dir}/../config/${instance}.kubeconfig"
done
```

**单个的生成config文件的代码：**

**work-0**

```
 kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority="./certificates_inuse/ca.pem" \
    --embed-certs=true \
    --server=https://192.168.199.40:6443 \
    --kubeconfig="./config/worker-0.kubeconfig"

  kubectl config set-credentials system:node:worker-0 \
    --client-certificate="./certificates_inuse/worker-0.pem" \
    --client-key="./certificates_inuse/worker-0-key.pem" \
    --embed-certs=true \
    --kubeconfig="./config/worker-0.kubeconfig"

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:worker-0 \
    --kubeconfig="./config/worker-0.kubeconfig"

  kubectl config use-context default --kubeconfig="./config/worker-0.kubeconfig"
```
**work-1**

```
 kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority="./certificates_inuse/ca.pem" \
    --embed-certs=true \
    --server=https://192.168.199.40:6443 \
    --kubeconfig="./config/worker-1.kubeconfig"

  kubectl config set-credentials system:node:worker-1 \
    --client-certificate="./certificates_inuse/worker-1.pem" \
    --client-key="./certificates_inuse/worker-1-key.pem" \
    --embed-certs=true \
    --kubeconfig="./config/worker-1.kubeconfig"

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:worker-1 \
    --kubeconfig="./config/worker-1.kubeconfig"

  kubectl config use-context default --kubeconfig="./config/worker-1.kubeconfig"
```

**work-2**


```
 kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority="./certificates_inuse/ca.pem" \
    --embed-certs=true \
    --server=https://192.168.199.40:6443 \
    --kubeconfig="./config/worker-2.kubeconfig"

  kubectl config set-credentials system:node:worker-2 \
    --client-certificate="./certificates_inuse/worker-2.pem" \
    --client-key="./certificates_inuse/worker-2-key.pem" \
    --embed-certs=true \
    --kubeconfig="./config/worker-2.kubeconfig"

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:worker-2 \
    --kubeconfig="./config/worker-2.kubeconfig"

  kubectl config use-context default --kubeconfig="./config/worker-2.kubeconfig"
```

