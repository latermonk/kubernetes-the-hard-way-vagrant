
```
./scripts/generate-kubeconfig-kube-proxy
./scripts/generate-kubeconfig-worker
```


## 01 generate-kubeconfig-kube-proxy


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




## 02 generate-kubeconfig-worker


```
for instance in worker-0 worker-1 worker-2; do
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority="../certificates/ca.pem" \
    --embed-certs=true \
    --server=https://192.168.199.40:6443 \
    --kubeconfig="../config/${instance}.kubeconfig"

  kubectl config set-credentials system:node:${instance} \
    --client-certificate="../certificates/${instance}.pem" \
    --client-key="../certificates/${instance}-key.pem" \
    --embed-certs=true \
    --kubeconfig="../config/${instance}.kubeconfig"

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:${instance} \
    --kubeconfig="../config/${instance}.kubeconfig"

  kubectl config use-context default --kubeconfig="../config/${instance}.kubeconfig"
done


```



