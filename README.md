# Kubernetes The Hard Way (Vagrant)

!!!  Great !!!

```
https://storage.googleapis.com/kubernetes-release/release/stable.txt

readonly k8s_version="v1.13.0"

readonly etcd_version="v3.3.9"

readonly cfssl_version="R1.2"

readonly traefik_version="v1.3.8"

readonly crio_version="v1.11.2"
```


##  系统架构

![k8s-arch00](_image/k8s-arch00.png)





**froked from :**     

https://github.com/kinvolk/kubernetes-the-hard-way-vagrant     
https://github.com/caicloud/kube-ladder/blob/master/tutorials/lab3-manual-installtion.md   


## Question

### 实际操作中会产生那些中间文件？

会产生如下文件：

```
tools/*


certificates/*pem
certificates/*csr


config/*kubeconfig
```
如果重新运行就需要把这些中间文件都删除掉。


### cfssl 和 cfssljson是什么?这两个工具怎么用？


```
cfssl 是CloudFlare的PKI工具集，可以用来生成Certificate Authority (CA) 和其它证书


```

```
cfssljson

从`cfssl`和`multirootca`程序获取JSON输出，并将证书，密钥，CSR和bundle写入磁盘

```


### 这个用的是k8s那个版本？

```
目前是 1.13
```

### 新生成的文件名和文件的作用是什么？


| 文件名 | 作用 | 注释 |
|---|---|---|
| ca-config.json | CA 配置文件 | 配置证书生成策略 |
| ca-csr.json | CA 凭证签发请求文件 | CA证书签名请求 |
| A3 | B3 | C3 |
| A4 | B4 | C4 |
| A5 | B5 | C5 |
| A6 | B6 | C6 |
| A7 | B7 | C7 |




----------------------------------------------------------------------------------------

 |
 |
 |
 |
 |
 |
\|/

## Setup  Manually

Remove previously created certificates, tools kubeconfig files:

```
./scripts/distclean
```

Download required tools and files:

```
./scripts/download-tools
```

Start the virtual machines (optionally, go drink a coffee or tee):

```
vagrant up
[...]
vagrant status

Current machine states:

controller-0              running (virtualbox)
controller-1              running (virtualbox)
controller-2              running (virtualbox)
worker-0                  running (virtualbox)
worker-1                  running (virtualbox)
worker-2                  running (virtualbox)
```

Generate the required certificates:

```
./scripts/generate-certs
```

Generate the kubeconfig files (as those include copies of the previously
generated certificates):

```
./scripts/generate-kubeconfig-kube-proxy
./scripts/generate-kubeconfig-worker
```

Setup etcd on the controller nodes and verify it has started:

```
./scripts/setup-etcd
[...]
vagrant ssh controller-0
ETCDCTL_API=3 etcdctl member list

6c500a9f4f9113de, started, controller-0, https://192.168.199.10:2380, https://192.168.199.10:2379
e206d150eae73959, started, controller-2, https://192.168.199.12:2380, https://192.168.199.12:2379
e7e775a3da74a469, started, controller-1, https://192.168.199.11:2380, https://192.168.199.11:2379
```

Setup the controller services and verify they are up and running:

```
./scripts/setup-controller-services
[...]
for c in controller-0 controller-1 controller-2; do vagrant ssh $c -- kubectl get componentstatuses; done

NAME                 STATUS    MESSAGE              ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-1               Healthy   {"health": "true"}
etcd-2               Healthy   {"health": "true"}
etcd-0               Healthy   {"health": "true"}
[...]
```

Create `ClusterRole`'s for kubelet API auth:

```
./scripts/setup-kubelet-api-cluster-role
```

Setup the worker binaries, services and configuration:

```
./scripts/setup-worker-services
[...]
vagrant ssh controller-0
kubectl get nodes

NAME       STATUS    AGE       VERSION
worker-0   Ready     1m        v1.3.0
worker-1   Ready     55s       v1.3.0
worker-2   Ready     12s       v1.3.0
```

Configure a `kubernetes-the-hard-way` context on your host, set it as
default and verify everything is ok:

```
./scripts/configure-kubectl-on-host

kubectl get componentstatuses
[...]
kubectl get nodes
[...]
```

## Using the cluster

### Setup DNS add-on

Deploy the DNS add-on and verify it's working:

```
kubectl create -f ./manifests/kube-dns.yaml
[...]
kubectl get pods -l k8s-app=kube-dns -n kube-system
[...]
kubectl run busybox --image=busybox:1.28 --command -- sleep 3600
[...]
POD_NAME=$(kubectl get pods -l run=busybox -o jsonpath="{.items[0].metadata.name}")
kubectl exec -ti $POD_NAME -- nslookup kubernetes
```

### Smoke tests

```
kubectl create -f ./manifests/nginx.yaml
deployment "nginx" created
service "nginx" created

NODE_PORT=$(kubectl get svc nginx --output=jsonpath='{range .spec.ports[0]}{.nodePort}')
for i in {0..2}; do curl -sS 192.168.199.2${i}:${NODE_PORT} | awk '/<h1>/{gsub("<[/]*h1>", ""); print $0}'; done
Welcome to nginx!
Welcome to nginx!
Welcome to nginx!
```

### Connect to services from host

`10.32.0.0/24` is the IP range for services. In order to connect to a service
from the host, one of the worker nodes (with `kube-proxy`) must be used as a
gateway. Example:


```
# On Linux
sudo route add -net 10.32.0.0/24 gw 192.168.199.22

# On macOS
sudo route -n add -net 10.32.0.0/24 192.168.199.22
```

### Use [Traefik](https://traefik.io/) loadbalancer

```
./scripts/setup-traefik
[...]
curl 192.168.199.30
404 page not found
```

To test traefik is actually doing its job, you can create an ingress rule
for the nginx service that you created above:

```
kubectl apply -f ./manifests/nginx-ingress.yaml
echo "192.168.199.30 nginx.kthw" | sudo tee -a /etc/hosts
curl nginx.kthw
<!DOCTYPE html>
[...]
```

