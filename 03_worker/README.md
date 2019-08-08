# worker-node组件   

**组件**    (https://kubernetes.io/zh/docs/concepts/overview/components/)    


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
| kubelet | /usr/local/bin/kubelet | C1 |
| kubelet-config.yaml |/var/lib/kubelet/ | C2 |
| kubelet.service | /etc/systemd/system/ | C3 |
|kubeconfig | /var/lib/kubelet/ | C4 |
| A5 | B5 | C5 |
| A6 | B6 | C6 |
| A7 | B7 | C7 |


# kubelet-config.yaml 


```
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    enabled: true
  x509:
    clientCAFile: "/var/lib/kubernetes/ca.pem"
authorization:
  mode: Webhook
clusterDomain: "cluster.local"
clusterDNS:
- "10.32.0.10"
podCIDR: "10.20.0.0/16"
resolvConf: "/run/systemd/resolve/resolv.conf"
runtimeRequestTimeout: "10m"
tlsCertFile: "/var/lib/kubelet/worker-0.pem"
tlsPrivateKeyFile: "/var/lib/kubelet/worker-0-key.pem"

```


#  kubelet.service

```
[Unit]
Description=Kubernetes Kubelet
Documentation=https://github.com/GoogleCloudPlatform/kubernetes
After=crio.service
Requires=crio.service

[Service]
ExecStart=/usr/local/bin/kubelet \
  --config=/var/lib/kubelet/kubelet-config.yaml \
  --container-runtime=remote \
  --container-runtime-endpoint=unix:///var/run/crio/crio.sock \
  --image-pull-progress-deadline=2m \
  --image-service-endpoint=unix:///var/run/crio/crio.sock \
  --kubeconfig=/var/lib/kubelet/kubeconfig \
  --network-plugin=cni \
  --register-node=true \
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target

```

# kubeconfig

```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUR4RENDQXF5Z0F3SUJBZ0lVQmR1QXhoTlcrYVppM1VLNHNpT21rSkxxVHBrd0RRWUpLb1pJaHZjTkFRRUwKQlFBd2FERUxNQWtHQTFVRUJoTUNWVk14RHpBTkJnTlZCQWdUQms5eVpXZHZiakVSTUE4R0ExVUVCeE1JVUc5eQpkR3hoYm1ReEV6QVJCZ05WQkFvVENrdDFZbVZ5Ym1WMFpYTXhDekFKQmdOVkJBc1RBa05CTVJNd0VRWURWUVFECkV3cExkV0psY201bGRHVnpNQjRYRFRFNU1EZ3dPREEzTVRrd01Gb1hEVEkwTURnd05qQTNNVGt3TUZvd2FERUwKTUFrR0ExVUVCaE1DVlZNeER6QU5CZ05WQkFnVEJrOXlaV2R2YmpFUk1BOEdBMVVFQnhNSVVHOXlkR3hoYm1ReApFekFSQmdOVkJBb1RDa3QxWW1WeWJtVjBaWE14Q3pBSkJnTlZCQXNUQWtOQk1STXdFUVlEVlFRREV3cExkV0psCmNtNWxkR1Z6TUlJQklqQU5CZ2txaGtpRzl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUEzVmxpT2dNNkUySTkKeTlabmMyQXVvamNBZ1VzcVRJMHgwRnhxemVZRXZXMU1zSVIrd2ZjVDJEOENnQk5hWk1vREFJTW0rcGtkQ3hRcQpNcml2MTB3RXJmME8xQWtxZUpYWEtjV0owM1BxYVErQU5BOFphT1htbTZ1UE5ibGw5cllDSWkvbGVzTmNOM1lXCkFRWUhnVUxNN3lrR0IrOWtWeVdTVk1IN3JBMmVQMm0wVGZTek0ySkJDS3BoUENCVWRyamR5Q25WQUYzS0h3dXUKWGh0UjFIbUlmSWtmaU1oMVE4QUppYmhBenB2SWtDWWZoT0tMd1Q4Y3FrYlgwcUxFZ3M4b096OU1sUDJzVWFvMgprQ282OUFSWXJTN1B5ZzRsWXBEdm15TVhjVFRMSTB0d1BocEt0R1ExMm5hbjNXYnZYNkFKOHNGdkp1MWtMM3J1Ck5oanA0cTY2V3dJREFRQUJvMll3WkRBT0JnTlZIUThCQWY4RUJBTUNBUVl3RWdZRFZSMFRBUUgvQkFnd0JnRUIKL3dJQkFqQWRCZ05WSFE0RUZnUVVmNmZLaERvTVJ0Y0lmU05UY2FuRzRuWmc2d293SHdZRFZSMGpCQmd3Rm9BVQpmNmZLaERvTVJ0Y0lmU05UY2FuRzRuWmc2d293RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUt3cEowRkFxL3p6ClZScFFTQlVDSlFybmRZbHZCQTBqVFo5UlNXdGhENFJNZmNPQTBhZGdFa0lUQkxJRTJ5d2E2STdRQUJDWWF3ZUoKNmVjK29OR2NjcU1iZHhHUnlZZ25rQ2pibE5NUi8zK3RhZnlHV05mRlQ3cU5LT1pHb1pZamVGR2JEYkU0TXkxRgpEcGdHS3pwb21RS291Y2RiLzFhMU5oMDZYdzdKZ0NvU1lXeVJSb0xZcmRnS29DTXZwd2hyZ1ZWczhpUHgva0ZnCnFOZW1sTzFoR0xPb3EyQkx6RmcwOGU4U1ZWeTh6b2hGNVFRSUpVWkZFNHp3ajVUdHFOcEJUZk1DWEk3VmZKckoKcEMrWjEzOXNuMDBDblowRi9VOTE3K3JHUGtEM1JBSmVNRU1kQ2hLNnYvYVErZDVQOWt5OUlpK1p4N05vR2dMVwpBRnF2SGJFTk1rTT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://192.168.199.40:6443
  name: kubernetes-the-hard-way
contexts:
- context:
    cluster: kubernetes-the-hard-way
    user: system:node:worker-0
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: system:node:worker-0
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVIRENDQXdTZ0F3SUJBZ0lVUkc0T01xdDJXeGNpUENGZXNaUU5sWE1qdjVNd0RRWUpLb1pJaHZjTkFRRUwKQlFBd2FERUxNQWtHQTFVRUJoTUNWVk14RHpBTkJnTlZCQWdUQms5eVpXZHZiakVSTUE4R0ExVUVCeE1JVUc5eQpkR3hoYm1ReEV6QVJCZ05WQkFvVENrdDFZbVZ5Ym1WMFpYTXhDekFKQmdOVkJBc1RBa05CTVJNd0VRWURWUVFECkV3cExkV0psY201bGRHVnpNQjRYRFRFNU1EZ3dPREEzTVRrd01Gb1hEVEl3TURnd056QTNNVGt3TUZvd2dZa3gKQ3pBSkJnTlZCQVlUQWxWVE1ROHdEUVlEVlFRSUV3WlBjbVZuYjI0eEVUQVBCZ05WQkFjVENGQnZjblJzWVc1awpNUlV3RXdZRFZRUUtFd3h6ZVhOMFpXMDZibTlrWlhNeElEQWVCZ05WQkFzVEYwdDFZbVZ5Ym1WMFpYTWdWR2hsCklFaGhjbVFnVjJGNU1SMHdHd1lEVlFRREV4UnplWE4wWlcwNmJtOWtaVHAzYjNKclpYSXRNRENDQVNJd0RRWUoKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTWc5dDF5cndUYkwvYWZqQXgrbWxpa1R6eWNFTTdRagp5S0cyMzczazkxSTFwNmlFTG5pc09jSXRkL0dSWGwyNFFWVTd6YTBXcTZibkxOK25rSDNxQUtjbmN2WmtVQXJ1CnVabkk3TVJnUXcwNlp5TlN5WU1mVks1cjdZZTF2Z0Q3cUxGTXRHa1JEOGsrRFBUY2pFbGtISjIwU0t5Z0toTXcKejFCNmpqb2VHL1RYZUlaWkFTM3dKWUFkajFhOVBQeEttNmFwOW5nWVN5SDJyS2lRVnVXM2l4bjdQak5La294YQpzN0g3VVhwN0dHWFdqVithQnMxclQyMDdGc2FZdkV6d2dSVnRGM0J5OVFIMEZWOGpja1MvcDVqRlhnR043NGlkCnBDd1pyL3MxSkVBQnRvNHcrcFdjdGg1WUhYTkw5dkpGM1ZOUms0ZlJndG9LWGdXOVpRWEgxT0VDQXdFQUFhT0IKbXpDQm1EQU9CZ05WSFE4QkFmOEVCQU1DQmFBd0hRWURWUjBsQkJZd0ZBWUlLd1lCQlFVSEF3RUdDQ3NHQVFVRgpCd01DTUF3R0ExVWRFd0VCL3dRQ01BQXdIUVlEVlIwT0JCWUVGRHREdGk1ZEJPS3ltSzR5YUh5b2REL0t1bG9lCk1COEdBMVVkSXdRWU1CYUFGSCtueW9RNkRFYlhDSDBqVTNHcHh1SjJZT3NLTUJrR0ExVWRFUVFTTUJDQ0NIZHYKY210bGNpMHdod1RBcU1jVU1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQnNVVGpBdzdTOW9xOTJOSFRqbVBwbAp3cHQrYnEwRlVwVThlOFp6dDl0aFI0SFZPZlNJalMzaEFFN21jc21HNm9IWURGcVA0Lzg0V0ZnRnVLS285ZGhtCnJESmdJTGc5YWtlaDJycXBLYTVQbmZCaXdTS21TT1N4Y3lTbzV6Tk1uMHM2eU05dXRMQ0t0VnVEUVRVSllPWloKYVFaMTl3di93THNPUXBWWXUybHNGS3ZudzVLalpXTkIvUFYwZG1KTGQ3UTZjK1hmT3V4akYwVEZkcmtmelkvbQpYa3hRM2pFcWlMWDJZSEtQd1lvL1JaeWwxSkZ1anZNbmZKQTdDU1lCMGVpUDFaK1BjQkE0b2Flc2E0YjRxZ2diCldHczlMRnVNTEk3emlxWTNDeTh6eXAwY0tNL1Z0b0VWaHJoM2JOYWxHMUxMZkhWd3BjVlJpZm8rdUF1dTBoM04KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcFFJQkFBS0NBUUVBeUQyM1hLdkJOc3Y5cCtNREg2YVdLUlBQSndRenRDUElvYmJmdmVUM1VqV25xSVF1CmVLdzV3aTEzOFpGZVhiaEJWVHZOclJhcnB1Y3MzNmVRZmVvQXB5ZHk5bVJRQ3U2NW1janN4R0JERFRwbkkxTEoKZ3g5VXJtdnRoN1crQVB1b3NVeTBhUkVQeVQ0TTlOeU1TV1FjbmJSSXJLQXFFekRQVUhxT09oNGI5TmQ0aGxrQgpMZkFsZ0IyUFZyMDgvRXFicHFuMmVCaExJZmFzcUpCVzViZUxHZnMrTTBxU2pGcXpzZnRSZW5zWVpkYU5YNW9HCnpXdFBiVHNXeHBpOFRQQ0JGVzBYY0hMMUFmUVZYeU55Ukwrbm1NVmVBWTN2aUoya0xCbXYrelVrUUFHMmpqRDYKbFp5MkhsZ2RjMHYyOGtYZFUxR1RoOUdDMmdwZUJiMWxCY2ZVNFFJREFRQUJBb0lCQVFDR3hHTEFVQ2grQk5UMwpSV1k0eXhtTkUvUDd3bXc1OVV0MjYybXltSU9NSk9odUZtaGdkeWw0Y3Jtakw3OWZQRDljYWgrSE94YlVBZ3JGCmpEVkhTVktmTFlIR0tzVmtWajg4TFdxTTBHelFYbHBELzlmNEZBSWVVaTFQaFJZWCswZ1I3Qm9RREplZlRjL1MKalEwdDU5OEtsT2ZFcHhjcVMxTjhTcittaGRVeTl4Qlo2RGY3L1kwMHJMUnY5Mzh2Rk5aeEJBRU5kR0dBRFNQYgpGQTFhTEtEUTJHZ0gxVTZDZ0FKZXUyNzdTcmlvbkVjSFFFOFJxbEZUM0ZOQ05tQlhtMGJTWitXOXFSeEQ1ZzNMCkdnejlUSXppSmlrVDZsODdsM1pIRHZnYWtKVkxvRGJOdWdUNVRORlM1bGlWNnNhYUxUWXM1b2p2NHlock5MbksKeVczQXRwWXhBb0dCQVBRZmlKcmpoQkd5ZU9sOXNFZUJaVkdwMFA5cDBJZUd3WFRXbndLbjJtcU1wVlBvOGRCeApRUlMwK3FLTzg0cFFNNVJLeWlHZHFDWCtqRURUQU5iemVEdmQwV1grcVRqclpOOGNnekZ2Q2FKb0Zmb3NnTzNzCnFTQWR4MGVlVzNDMVZCNVNJQ2g2enVwdHBkTkFkeFFLRXgrNXlDRDdRZGY3OSs5cjgwcjMwcHdmQW9HQkFOSDcKcFpvYlpzRUd1RFpXdDhNV1FWcVRnT1ZQZkNzSHFCRWVRTjBiMTFvd0w5d0kyT3N5NTZIcnVEMSt6T0Nrd1ppOQpHMXN1Ukx3WXhtMlZBTDhmOTNTUlRFdGhDRzNrUXJxKzdJb25aZld3aWkzN25ZZDdwWHFGMmo5eW1YekdTWkV3CllpY3J6NFdJcktiQ0dZSlM4ZjFvVnEvcTNPMTdvVzlxaHRlMjkyNy9Bb0dCQUlXTUtxTHhDL0xTZTZyZ0EydGEKc2g2Mms0cm1UQm1YOG9VRVZBQ1Q2cnlYTEdwMjZMSnJBbnRkZGk4dm5MTnhwSVhmVkN5SG50RUtLNWZhMW43QQp1bW1sczBQa3lKWkd6K3Z1SVNjTjQydGhIWVM2aTkyY3RmakgwWml3WEwyUHRQRFJaSjVBY0tsMlNsMEN5eUcvClVTN3Q1em1EdWtWNXBlem9WVFFSMXJscEFvR0FNSzNPK1VmcTdVcTZmOVpncVhoS1ZFNG5yb0ZidnJuNnEyVkIKRWtFM0RuQm5WL0l4SDIxZ3AzZjI4VUtwQ2xjSGpub2drbVhLaDNTUzQ2cU1Uc0tCWW1PZ3RVVGNhR1ZuVnRXVwpEbjBOdFFSbE5EM0ZpamdPNTdXS0gvVllRY1hTSGplQ09hUjlhbTZCMy9MVmFSSURpWVNidkJLTmZ2eVBNejRWCjNvSkxJRThDZ1lFQTAyVHlLVzZia3loQWNrb2pBU0FqcVZqOHN5Z0FZazdMYXJuSkFOdTFGWjhBbnNzbmFZVXYKOWc4TEp3SmJuRlVDN2l0Q2JXZGVyUCthd3ptU2gwUGdacG00QlVBZzJEWGRnME1CYm9yUHczVG5raDBLYWx3eQphMnI5cUhYaytOVGpRQ2hpbEpPNzhiN2VHbHRjbmNUd3pKSnFPR2tuK1I3Y0pYaUJrTk5VVEE4PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

```



##  kube-proxy
[kube-proxy](https://kubernetes.io/docs/admin/kube-proxy)通过维护主机上的网络规则并执行连接转发，实现了Kubernetes服务抽象



| A0 | B0 | C0 |
|---|---|---|
| kube-proxy | /usr/local/bin/ | C1 |
| kubeconfig | /var/lib/kube-proxy/ | C2 |
| kube-proxy.service | B3 | C3 |
| A4 | B4 | C4 |
| A5 | B5 | C5 |
| A6 | B6 | C6 |
| A7 | B7 | C7 |



# kube-proxy.service


```
[Unit]
Description=Kubernetes Kube Proxy
Documentation=https://github.com/GoogleCloudPlatform/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-proxy \
  --cluster-cidr=10.200.0.0/16 \
  --kubeconfig=/var/lib/kube-proxy/kubeconfig \
  --proxy-mode=iptables \
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target

```


