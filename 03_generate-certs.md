
```
./generate-ca
./generate-admin-cert
./generate-worker-certs
./generate-kube-proxy-cert
./generate-kubernetes-cert
```


## generate-ca


```
cfssl gencert -initca ca-csr.json | cfssljson -bare ca
```


## generate-admin-cert


```
cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  admin-csr.json | cfssljson -bare admin
```


## generate-worker-certs


```
for i in {0..2}; do
  cfssl gencert \
    -ca=ca.pem \
    -ca-key=ca-key.pem \
    -config=ca-config.json \
    -hostname="worker-${i},192.168.199.2$((i))" \
    -profile=kubernetes \
    "worker-${i}-csr.json" | cfssljson -bare "worker-${i}"
done
```


## generate-kube-proxy-cert


```
cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -profile=kubernetes \
  kube-proxy-csr.json | cfssljson -bare kube-proxy
```


## generate-kubernetes-cert


```

cfssl gencert \
  -ca=ca.pem \
  -ca-key=ca-key.pem \
  -config=ca-config.json \
  -hostname=10.32.0.1,192.168.199.40,192.168.199.10,192.168.199.11,192.168.199.12,127.0.0.1,kubernetes.default \
  -profile=kubernetes \
  kubernetes-csr.json | cfssljson -bare kubernetes
```

