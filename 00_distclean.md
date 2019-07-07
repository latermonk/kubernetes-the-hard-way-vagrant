# CLEAN
##  删除二进制文件
```

rm -vfr tools/*

```

## 删除秘钥
```

rm -vf certificates/*pem
rm -vf certificates/*csr
```

## 删除config文件
```

rm -vf config/*kubeconfig
```



# INSTALL TOOLS


## CFSSL

```
wget -q --show-progress --https-only --timestamping \
  https://pkg.cfssl.org/R1.2/cfssl_linux-amd64 \
  https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64

chmod +x cfssl_linux-amd64 cfssljson_linux-amd64

sudo mv cfssl_linux-amd64 /usr/local/bin/cfssl

sudo mv cfssljson_linux-amd64 /usr/local/bin/cfssljson

```

```
cfssl version

```


## KUBECTL

```

wget https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kubectl    

chmod +x kubectl   

sudo mv kubectl /usr/local/bin/   

```

check:

```
kubectl version --client

```
