## 简介
因为我们 kubernetes 版本为 v1.6.2 所以我们选择对应的 Dashboard 版本为 v1.6.+ 即可，版本也不要太新，否则可能会出现兼容性问题。不过可以使用 v1.6.0+ 的版本，它开始支持中文了，更直观一些哈。
```shell
kubectl create -f kubernetes-dashboard.yaml
```

## 创建nginx-rc.yaml文件
## 查看token
```shell
kubectl -n kube-system describe $(kubectl -n kube-system get secret -n kube-system -o name | grep namespace) | grep token
kubectl get po,svc -n kube-system -o wide
kubectl logs pod/heapster-7c9cff9f8-lshmn -n kube-system
kubectl logs pod/kubernetes-dashboard-78dc5f9d6b-r84z5  -n kube-system
kubectl logs pod/monitoring-grafana-77db8c878f-b5t4h   -n kube-system
kubectl logs pod/monitoring-influxdb-7f7f87658-kc2x9  -n kube-system

```

错误1：这个代表heapster没有创建.
```shell
[root@master dashboard]# kubectl logs pod/kubernetes-dashboard-57c65ffd69-nx6r6 -n kube-system
2018/09/21 03:00:16 Metric client health check failed: the server could not find the requested resource (get services heapster). Retrying in 30 seconds.
```
错误2：
```shell
2018/09/21 03:01:46 Metric client health check failed: an error on the server ("[-]healthz failed: could not get the latest data batch\nhealthz check failed") has prevented the request from succeeding (get services heapster). Retrying in 30 seconds.
```

访问方式
```shell
方式1：
kubectl proxy --address='0.0.0.0'  --accept-hosts='^*$'
http://120.79.189.147:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
http://193.112.125.239:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

方式2：
--test-type --ignore-certificate-errors
```

## 安装插件
### 整合heapster和influxdb

https://120.79.189.147:6443/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
https://193.112.125.239:6443/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/



# 参考文档
- https://www.cnblogs.com/RainingNight/p/deploying-k8s-dashboard-ui.html  kubernetes-dashboard(1.8.3)部署与踩坑
- http://blog.51cto.com/ylw6006/2113542 K8S使用dashboard管理集群
- http://www.cnblogs.com/scode2/p/8810052.html 详解k8s一个完整的监控方案(Heapster+Grafana+InfluxDB) - kubernetes
- http://www.mamicode.com/info-detail-2247805.html  详解k8s一个完整的监控方案(Heapster+Grafana+InfluxDB) - kubernetes
- https://blog.csdn.net/aixiaoyang168/article/details/78411511 国内使用 kubeadm 在 Centos 7 搭建 Kubernetes 集群