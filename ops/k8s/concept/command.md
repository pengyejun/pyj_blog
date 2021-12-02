## 1. Node相关
查看所有节点的列表：

    kubectl get nodes -o wide
查看节点状态以及节点的其他详细信息：  

    kubectl describe node <your-node-name>


## 2.etcd

查看etcd中存储了哪些键

    ETCDCTL_API=3 etcdctl --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt --key=/etc/kubernetes/pki/etcd/healthcheck-client.key get / --keys-only --prefix
