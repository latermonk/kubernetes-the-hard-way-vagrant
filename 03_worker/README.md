# worker-node组件   

**组件**    
[https://kubernetes.io/zh/docs/concepts/overview/components/]       (https://kubernetes.io/zh/docs/concepts/overview/components/)    


[kubelet](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/)
[kube-proxy](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/)

##  kubelet

kubelet是主要的节点代理,它监测已分配给其节点的 Pod(通过 apiserver 或通过本地配置文件)，提供如下功能:

*   挂载 Pod 所需要的数据卷(Volume)。
*   下载 Pod 的 secrets。
*   通过 Docker 运行(或通过 rkt)运行 Pod 的容器。
*   周期性的对容器生命周期进行探测。
*   如果需要，通过创建 _镜像 Pod（Mirror Pod）_ 将 Pod 的状态报告回系统的其余部分。
*   将节点的状态报告回系统的其余部分。



| 文件 | 位置 | 备注 |
|---|---|---|
| kubelet | B1 | C1 |
| A2 | B2 | C2 |
| kubelet.service | B3 | C3 |
| A4 | B4 | C4 |
| A5 | B5 | C5 |
| A6 | B6 | C6 |
| A7 | B7 | C7 |
#  kubelet.service





##  kube-proxy
[kube-proxy](https://kubernetes.io/docs/admin/kube-proxy)通过维护主机上的网络规则并执行连接转发，实现了Kubernetes服务抽象



| A0 | B0 | C0 |
|---|---|---|
| A1 | B1 | C1 |
| A2 | B2 | C2 |
| kube-proxy.service | B3 | C3 |
| A4 | B4 | C4 |
| A5 | B5 | C5 |
| A6 | B6 | C6 |
| A7 | B7 | C7 |



# kube-proxy.service

