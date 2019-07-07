
```
./scripts/configure-kubectl-on-host
```
## step01


```

kubectl config set-cluster kubernetes-the-hard-way \
  --certificate-authority="../certificates/ca.pem" \
  --embed-certs=true \
  --server=https://192.168.199.40:6443

```

```

kubectl config set-credentials admin \
  --client-certificate="../certificates/admin.pem" \
  --client-key="${dir}/../certificates/admin-key.pem"

```


```

kubectl config set-context kubernetes-the-hard-way \
  --cluster=kubernetes-the-hard-way \
  --user=admin


```

```

kubectl config use-context kubernetes-the-hard-way

```



## step02
```


kubectl get componentstatuses


```

```
kubectl get nodes
```

