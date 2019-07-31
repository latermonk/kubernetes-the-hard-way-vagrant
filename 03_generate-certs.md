# check the scripts 
使用 CloudFlare's PKI 工具 [cfssl](https://github.com/cloudflare/cfssl) 来配置 [PKI Infrastructure](https://en.wikipedia.org/wiki/Public_key_infrastructure)，然后使用它去创建 Certificate Authority（CA），并为 etcd、kube-apiserver、kubelet 以及 kube-proxy 创建 TLS 证书。



```
cat  ./scripts/generate-certs
```

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
其中：
**ca-csr.json**

```
{
  "CN": "Kubernetes",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "US",
      "L": "Portland",
      "O": "Kubernetes",
      "OU": "CA",
      "ST": "Oregon"
    }
  ]
}

```
**执行命令行后生成以下文件**

```
ca-key.pem（私钥）

ca.pem（证书）

还会生成ca.csr（证书签名请求），用于交叉签名或重新签名。


```
**查看证书信息**

```
 cfssl certinfo -cert ca.pem
```
**查看CSR(证书签名请求)信息：**

```
cfssl certinfo -csr ca.csr
```
## 配置证书生成策略
**配置证书生成策略，让CA软件知道颁发什么样的证书。**


**ca-config.json**

```
{
  "signing": {
    "default": {
      "expiry": "8760h"
    },
    "profiles": {
      "kubernetes": {
        "usages": ["signing", "key encipherment", "server auth", "client auth"],
        "expiry": "8760h"
      }
    }
  }
}

```

```
这个策略，有一个默认的配置，和一个profile，可以设置多个profile，这里的profile是etcd。

    默认策略，指定了证书的有效期是一年(8760h)

    etcd策略，指定了证书的用途

    signing, 表示该证书可用于签名其它证书；生成的 ca.pem 证书中 CA=TRUE

    server auth：表示 client 可以用该 CA 对 server 提供的证书进行验证

    client auth：表示 server 可以用该 CA 对 client 提供的证书进行验证
```
**cfssl常用命令：**

*   `cfssl gencert -initca ca-csr.json | cfssljson -bare ca ## 初始化ca`

*   `cfssl gencert -initca -ca-key key.pem ca-csr.json | cfssljson -bare ca ## 使用现有私钥, 重新生成`

*   `cfssl certinfo -cert ca.pem`

*   `cfssl certinfo -csr ca.csr`



参考文件：
http://blog.itpub.net/28624388/viewspace-2152064/

-----------


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

